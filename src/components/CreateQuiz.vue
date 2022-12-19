<template>
    <div class="card">
        <div class="alert alert-danger" v-if="isCorrectAnswerClicked ">Lütfen sorunuzun cevabını işaretleyiniz.</div>
        <div class="d-flex justify-content-between align-items-center card-header py-1">
            <div>
                <button @click="currentIndex--" class="btn btn-sm btn-outline-secondary me-2 mt-2"
                    :disabled="!currentIndex">Önceki
                    Soru</button>
                <button @click="currentIndex++" class="btn btn-sm btn-outline-success mt-2"
                    :disabled="!questions[currentIndex+1]">Sonraki Soru</button>
            </div>
            <button @click="emits('createQuiz')" class="btn btn-primary btn-sm " :disabled="!createQuizRules">
                Quizi Oluştur
            </button>
        </div>
        <div class="d-flex justify-content-between align-items-center card-header">
            <h5>
                {{currentQuestionCount}}. Soru / <small>{{questions.length}}</small>
            </h5>
            <div class="text-end">
                <button @click="deleteQuestion" class="btn btn-sm btn-outline-danger ms-2 mb-2"
                    :disabled="questions.length<2">
                    Soruyu Sil
                </button>
                <button @click="addQuestion" class="btn btn-sm btn-outline-primary ms-2 mb-2">
                    {{ questions.length === 15 ? 'Maksimum 15 soru ekleyebilirsin' : 'Soru Oluştur' }}
                </button>
            </div>
        </div>
        <div class="card-body">
            <input v-model="currentQuestion.question" type="text" class="form-control"
                placeholder="Örnek: İlk evcil hayvanımın adı neydi?" />

            <div v-for="(option,key) in currentQuestion.options" :key="key" class="input-group mb-3 mt-3">
                <div class="input-group-text">
                    <input v-model="option.is_true" @change="emits('checkTrueOption',currentIndex,key)"
                        class="form-check-input  mt-0" type="checkbox">
                </div>
                <input v-model="option.value" type="text" class="form-control" :placeholder="(key+1)+'. Şık'">
                <button v-if="currentQuestion.options.length > 2" @click="emits('deleteOption',currentIndex,key)"
                    class="btn btn-outline-danger" type="button">
                    <i class="bi bi-trash" />
                    Sil
                </button>
            </div>
            <button @click="emits('addOption',currentIndex)" :disabled="maxOptionsCountRules"
                class="btn w-100 text-center btn-outline-success">
                {{maxOptionsCountRules ? 'Maksimum 6 Şık Eklenebilir' : 'Şık Ekle'}}
            </button>
        </div>
    </div>
</template>

<script setup>
    import {
        computed,
        ref
    } from 'vue'

    const emits = defineEmits(['addOption', 'deleteOption', 'checkTrueOption', 'addQuestion', 'deleteQuestion',
        'createQuiz'
    ])

    const props = defineProps({
        questions: Array
    })
    const currentIndex = ref(0)
    const isCorrectAnswerClicked = ref(false)
    const currentQuestion = computed(() => props.questions[currentIndex.value])
    const currentQuestionCount = computed(() => (currentIndex.value + 1))
    const maxOptionsCountRules = computed(() => currentQuestion.value.options.length > 5)
    const newQuestionRules = computed(() => {
        const optionsIsFilled = currentQuestion.value.options.every(option => option.value.length)
        const checkboxIsChecked = currentQuestion.value.options.some(option => option.is_true)
        return currentQuestion.value.question.length > 2 && optionsIsFilled && checkboxIsChecked && props
            .questions.length < 15
    })
    const createQuizRules = computed(() => {
        return props.questions.every(question => {
            const optionsIsFilled = question.options.every(option => option.value.length)
            const checkboxIsChecked = question.options.some(option => option.is_true)
            return question.question.length > 4 && optionsIsFilled && checkboxIsChecked && props
                .questions.length > 1
        })
    })

    const addQuestion = () => {
        if (!newQuestionRules.value) {
            isCorrectAnswerClicked.value = true
            return
        }
        isCorrectAnswerClicked.value = false
        emits('addQuestion')
        currentIndex.value++
    }
    const deleteQuestion = () => {
        emits('deleteQuestion', currentIndex)
        currentIndex.value ? currentIndex.value-- : currentIndex.value = 0
    }
</script>