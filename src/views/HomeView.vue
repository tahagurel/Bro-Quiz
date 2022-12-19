<template>
  <div class="p-3 bg-light border rounded-3">
    <div v-if="!quizData.name">
      <h2>Kendinle İlgili Quizler Oluştur</h2>
      <p>
        Arkadaşların seni ne kadar iyi tanıyor? Öğrenmek için kendinle ilgili sorular oluştur.
        Quiz linkini arkadaşlarınla paylaş puanlarını görüntüle.
        Hemen başlamak için adını yazıp 'Başla' butonuna tıkla.
      </p>
      <hr />
    </div>

    <template v-if="!quizId">
      <NameInput v-if="!quizData.name.length" @updateName="updateName" />
      <CreateQuiz v-else :questions="quizData.questions" @addQuestion="addQuestion" @deleteQuestion="deleteQuestion"
        @addOption="addOption" @deleteOption="deleteOption" @checkTrueOption="checkTrueOption"
        @createQuiz="createQuiz" />
    </template>
    <ShareQuiz v-else :quizId="quizId" />
  </div>
</template>

<script setup>
  import {
    setDoc,
    doc,
  } from 'firebase/firestore'
  import db from '@/firebase'
  import NameInput from '@/components/NameInput.vue'
  import CreateQuiz from '@/components/CreateQuiz.vue'
  import ShareQuiz from '@/components/ShareQuiz.vue'

  import {
    reactive,
    ref
  } from 'vue';


  const quizData = reactive({
    name: '',
    questions: [],
    results: []
  });

  const quizId = ref()

  const updateName = ({
    value
  }) => {
    quizData.name = value
    createQuestion();
  }

  const createQuestion = () => {
    quizData.questions.push({
      question: '',
      options: [{
          value: '',
          is_true: false
        },
        {
          value: '',
          is_true: false
        },
      ]
    })
  }

  const addOption = (index) => {
    quizData.questions[index].options.push({
      value: '',
      is_true: false
    })
  }

  const deleteOption = (questionKey, optionKey) => {
    quizData.questions[questionKey].options.splice(optionKey, 1)
  }

  const checkTrueOption = (questionKey, optionKey) => {
    quizData.questions[questionKey].options.map((option, key) => {
      return option.is_true = optionKey === key ? true : false
    })
  }

  const addQuestion = () => {
    createQuestion()
  }
  const deleteQuestion = (index) => {
    quizData.questions.splice(index, 1)
  }

  const createQuiz = () => {
    const id = quizData.name + '-' + new Date().getTime()
    setDoc(doc(db, "quizzes", id), quizData).then(() => {
      quizId.value = id
    })
  }
</script>

<style lang="scss">

</style>