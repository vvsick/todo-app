<template>
    <div>
        <button 
        class="button mt-2" @click="showModal = !showModal">
            Edit todo
        </button>

        <Teleport to="body">
            <div class="modal" :class="{ 'is-active': showModal }">
                <div class="modal-background" @click="closeModal"></div>
                <div class="modal-content">
                    <form @submit.prevent="updateTodo" class="box">
                        <span class="is-size-5">Edit todo</span>
                        <input class="input mt-2 mb-2" type="text" placeholder="Title" v-model="editedTodo.title">
                        <textarea class="textarea mb-2" type="text" placeholder="Description" v-model="editedTodo.description"></textarea>
                        <button class="button is-success mr-2" type="submit">Save</button>
                        <button class="button is-danger mr-2" type="button" @click="deleteTodo">Delete</button>
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
    </div>
</template>

<script setup lang="ts">
import { PropType, ref } from 'vue';
import Todo from '../types/Todo';

const emit = defineEmits(['update-todo', 'delete-todo'])

const props = defineProps({
    todo: {
        type: Object as PropType<Todo>,
        required: true
    }
});
const showModal = ref(false);
const editedTodo = ref({ ...props.todo });

const updateTodo = () => {
    emit('update-todo', editedTodo.value);
    showModal.value = false;
};

const deleteTodo = () => {
    emit('delete-todo', props.todo.id);
    showModal.value = false;
};

const closeModal = () => {
    showModal.value = false;
    editedTodo.value = { ...props.todo };
};

</script>

<style scoped>

</style>