<template>
    <div class="alert alert-success" role="alert">
        <h4 class="alert-heading">Quiz Oluşturuldu!</h4>
        <p>Aşağıdaki linkleri kullanarak quizini arkadaşlarınla paylaşabilirsin. Ayrıca quiz sayfasında
            katılan arkadaşlarının aldığı puanları görebilirsin.
        </p>
        <hr>
        <div class="input-group mb-3">
            <input v-on:focus="$event.target.select()" ref="clone" class="form-control" readonly :value="quizLink" />
            <button class="btn btn-primary" type="button" @click="copy">Kopyala</button>
        </div>
        <router-link :to="{ name: 'quiz', params: { id:quizId }}" class="btn btn-success btn-sm">
            Quiz'e Git
        </router-link>
    </div>
</template>

<script setup>
    import {
        onMounted,
        ref
    } from "vue"
    import {
        useRouter
    } from 'vue-router';


    const props = defineProps({
        quizId: String
    })

    const clone = ref()
    const quizLink = ref('')

    const copy = () => {
        clone.value.focus();
        document.execCommand('copy');
    }

    onMounted(() => {
        const route = useRouter().resolve({
            name: 'quiz',
            params: {
                id: props.quizId
            }
        })
        quizLink.value = new URL(route.href, window.location.origin).href
    })
</script>