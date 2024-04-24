<script setup lang="ts">
import AddTodo from './components/AddTodo.vue';
import EditTodo from './components/EditTodo.vue';
import Todo from './types/Todo';
import { computed, ref, watch } from 'vue';

const todos = ref<Todo[]>(JSON.parse(localStorage.getItem('todos') || '[]'));
const deleteMode = ref(false);
const filter = ref('All');
const filterItems: Array<string> = ['All', 'Completed', 'Incompleted'];

const sortedTodos = computed(() => {
  switch (filter.value) {
    case 'All':
      return todos.value;
    case 'Completed': 
      return todos.value.filter(todo => todo.completed);
    case 'Incompleted':
      return todos.value.filter(todo => !todo.completed);
  }
});

const addTodo = (todo: Todo) => {
  todos.value.push(todo);
  saveTodos();
};

const updateTodo = (todo: Todo) => {
  const index = todos.value.findIndex(t => t.id === todo.id);
  if (index !== -1) {
    todos.value.splice(index, 1, { ...todo });
    saveTodos();
  }
};

const deleteTodo = (id: number) => {
  const index = todos.value.findIndex(t => t.id === id);
  if (index !== -1) {
    todos.value.splice(index, 1);
    saveTodos();
  }
}

watch(deleteMode, () => {
  if (!deleteMode.value) {
    todos.value.forEach(todo => {
      todo.isSelected = false;
    })
    saveTodos();
  }
});

const deleteSelectedTodods = () => {
  todos.value = todos.value.filter(todo => !todo.isSelected);
  saveTodos();
}; 

const saveTodos = () => {
  localStorage.setItem('todos', JSON.stringify(todos.value));
};

</script>

<template>
  <div class="container">
    <nav class="level box">
        <div 
          v-for="(item, index) in filterItems"
          :key="index"
          class="level-item"
          @click="filter = item"
        >
          <a 
          :class="{'is-active': filter === item}"
          >
            {{ item }}
          </a>
        </div>
    </nav>
    <div class="section pt-4">
      <div class="mb-4">
        <AddTodo @add-todo="addTodo"/>
      </div>
      <div class="grid">
        <div class="cell">
          <button class="button" @click="deleteMode = !deleteMode">Delete mode</button>
        </div>
        <div class="cell">
          <Transition name="fade">
            <button class="button" @click="deleteSelectedTodods" v-if="deleteMode">Delete selected todos</button>
          </Transition>
        </div>
      </div>
      
      <TransitionGroup name="fadeDown">
        <div 
        v-for="(todo, index) in sortedTodos"
        :key="index"
        class="box-wrapper">
          <div class="box">
            <div class="columns">
              <div class="column content">
                <h5>{{ todo.title }}</h5>
                <span>{{ todo.description }}</span>
              </div>
              <div class="column right">
                <div class="fixed-grid has-2-cols">
                  <div class="grid">
                    <div class="cell">
                      <label class="checkbox">
                        <input type="checkbox" v-model="todo.completed" @change="saveTodos">
                        Done
                      </label>
                    </div>
                    <div class="cell">
                      <Transition name="fade">
                        <label class="checkbox" v-if="deleteMode">
                          <input type="checkbox" v-model="todo.isSelected" @change="saveTodos">
                          Select to delete
                        </label>
                      </Transition>
                    </div>
                    <div class="cell is-col-span-2">
                      <EditTodo :todo="todo" @update-todo="updateTodo" @delete-todo="deleteTodo"/>
                    </div>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </TransitionGroup>

    </div>
  </div>
</template>

<style lang="scss" scoped>
.button {
  width: 100%;
}

.content {
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}
</style>
