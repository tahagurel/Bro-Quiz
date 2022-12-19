<template>
    <div class="input-group input-group-lg mb-3">
        <input v-model="name" type="text" class="form-control" placeholder="Adınızı Yazınız">
        <button @click="startQuiz" class="btn btn-outline-secondary" type="button" :disabled="!name.length">
            Başla
        </button>
    </div>
</template>

<script setup>
    import {
        ref
    } from "vue";
    const emit = defineEmits(['updateName'])

    const name = ref('');

    const startQuiz = () => {
        emit('updateName', name);
    }
</script>

<style lang="scss" scoped>

</style>