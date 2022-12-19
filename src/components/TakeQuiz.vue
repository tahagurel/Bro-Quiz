<template>
    <div class="card">

        <div class="card-header pb-0 d-flex justify-content-between">
            <span class="h5">{{currentQuestion.question}}</span>
            <span class="h5">{{currentQuestionInfo}}</span>
        </div>
        <div class="card-body">
            <ul class="list-group">
                <li v-for="(option,key) in currentQuestion.options" :key="key" class="list-group-item">
                    <input v-model="option.answer" @change="answerChooseHandler(key)" class="form-check-input"
                        type="checkbox" value="" :id="key">
                    <label class="form-check-label ms-2 w-75" :for="key">{{option.value}}</label>
                </li>
            </ul>
        </div>
        <div class="card-footer d-flex justify-content-between">
            <div>
                <button @click="currentIndex--" :disabled="!currentIndex"
                    class="btn btn-sm btn-outline-danger me-2">Önceki Soru</button>
                <button @click="currentIndex++" :disabled="!questions[currentIndex+1]"
                    class="btn btn-sm btn-outline-primary">Sonraki Soru</button>
            </div>
            <button v-if="quizFinishRules" @click="emits('finishQuiz')" class="btn btn-sm btn-outline-success">Quizi
                Bitir</button>
        </div>
    </div>
</template>

<script setup>
    import {
        ref
    } from "vue"
    import {
        computed
    } from "vue"

    const props = defineProps({
        questions: Array
    })

    const emits = defineEmits(['finishQuiz'])

    const currentIndex = ref(0)
    const currentQuestion = computed(() => {
        return props.questions[currentIndex.value]
    })

    const answerChooseHandler = (choosedKey) => {
        props.questions[currentIndex.value].options.map((option, key) => {
            option.answer = choosedKey === key ? true : false
        })
    }

    const currentQuestionInfo = computed(() => {
        return (currentIndex.value + 1) + '/' + props.questions.length
    })

    const quizFinishRules = computed(() => {
        return props.questions.every((question) => {
            return question.options.some(option => option.answer)
        })
    })
</script>