<template>
    <div v-if="quiz" class="p-3 bg-light border rounded-3">
        <h2>{{quiz.name}} seni quize davet ediyor!</h2>
        <p>
            {{quiz.name}} sana <b>{{quiz.questions.length}}</b> sorudan oluşan quiz gönderdi. Arkadaşını ne kadar
            tanıdığını
            test etmek için adını
            yazıp
            Başla 'ya tıkla. Quiz sonunda aldığın puan adınla beraber burada görüntülenecek.
        </p>
        <hr />
        <TakeQuiz v-if="resultData.name" :questions="quiz.questions" @finishQuiz="finishQuiz" />
        <div v-else>
            <input v-model="name" type="text" class="form-control mb-2" placeholder="Adınızı Giriniz" />
            <button @click="joinQuiz" :disabled="!name.length" class="btn btn-sm btn-primary w-100">Quize'e
                Katıl</button>

            <div v-if="isQuizFinished" class="alert alert-success mt-3 mb-1 py-1">
                <i class="bi bi-check2-circle" />
                Quizi başarıyla tamamladın!
            </div>
            <div class="card mt-3">
                <h4 class="card-header">Sıralama Tablosu</h4>
                <div class="card-body p-0">
                    <ul v-if="quiz.results.length" class="list-group">
                        <li v-for="(result,key) in quiz.results" :key="key" class="list-group-item"><span class="h5">
                                <i v-if="!key" class="bi bi-trophy-fill text-warning" />
                                {{result.point}} Puan -</span> {{result.name}}</li>
                    </ul>
                    <div v-else class="alert alert-warning mb-0">Henüz quize kimse katılmadı.</div>
                </div>
            </div>
        </div>
    </div>
    <div v-else class="alert alert-warning" role="alert">
        <div class="spinner-border spinner-border-sm" role="status" />
        Yükleniyor...
    </div>
</template>
<script setup>
    import TakeQuiz from '@/components/TakeQuiz.vue'
    import {
        reactive,
        ref
    } from 'vue'
    import {
        useRoute
    } from 'vue-router'

    import db from '@/firebase'
    import {
        doc,
        onSnapshot,
        setDoc
    } from "firebase/firestore";
    import {
        onMounted
    } from 'vue';


    const route = useRoute()

    const name = ref('')
    const quiz = ref(null)
    const baseQuestions = ref([])
    const isQuizFinished = ref(false)

    const resultData = reactive({
        name: '',
        point: ''
    })

    const joinQuiz = () => {
        resultData.name = name.value
    }

    const finishQuiz = () => {
        const trueAnswerCount = quiz.value.questions.filter(question => {
            return question.options.find(option => option.is_true && option.answer)
        })

        const questionPoint = 100 / quiz.value.questions.length
        const quizPoint = Math.floor(trueAnswerCount.length * questionPoint)
        resultData.point = quizPoint
        quiz.value.results.push(resultData)
        quiz.value.questions = baseQuestions.value
        setDoc(doc(db, "quizzes", route.params.id), quiz.value).then(() => {
            name.value = ''
            resultData.name = ''
            resultData.point = ''
            isQuizFinished.value = true
        })
    }

    onMounted(async () => {
        onSnapshot(doc(db, "quizzes", route.params.id), (quizData) => {
            quiz.value = quizData.data()
            baseQuestions.value = quizData.data().questions
            quiz.value.results.sort((a, b) => b.point - a.point)
        });
    })
</script>