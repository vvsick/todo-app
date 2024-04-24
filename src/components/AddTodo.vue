<template>
    <button 
    class="button" @click="showModal = !showModal">
        Add todo
    </button>

    <Teleport to="body">
        <div class="modal" :class="{ 'is-active': showModal }">
            <div class="modal-background" @click="closeModal"></div>
            <div class="modal-content">
                <form @submit.prevent="addTodo" class="box">
                    <span class="is-size-5">Create todo</span>
                    <input class="input mt-2 mb-2" type="text" placeholder="Title" v-model="title" required>
                    <textarea class="textarea mb-2" type="text" placeholder="Description" v-model="description"></textarea>
                    <button class="button is-success mr-2" type="submit">Create</button>
                    <button class="button" type="button" @click="closeModal">Cancel</button>
                </form>
            </div>
    
            <button 
            class="modal-close is-large" 
            aria-label="close" 
            @click="closeModal">
            </button>
        </div>
    </Teleport>
</template>

<script setup lang="ts">
import { ref } from 'vue';
import Todo from '../types/Todo';

const emit = defineEmits(['add-todo'])

const showModal = ref(false);
const title = ref('');
const description = ref('');

const closeModal = () => {
    showModal.value = false;
    description.value = '';
    title.value = '';
}

const addTodo = () => {
    const todo: Todo = {
        id: Date.now(),
        title: title.value,
        description: description.value,
        completed: false,
        isSelected: false
    }
    title.value = '';
    description.value = '';
    emit('add-todo', todo);
}

</script>

<style scoped>
.button {
    width: 100%;
}
</style>