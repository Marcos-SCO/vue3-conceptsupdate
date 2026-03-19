<script setup>
import { ref, computed, watch, onMounted } from 'vue';

const newTask = ref('');
const tasks = ref([]);

function addTask() {
  const text = newTask.value.trim();

  if (!text) return;

  tasks.value.push({
    id: Date.now(),
    text,
    completed: false,
    favorite: false,
  });

  newTask.value = '';
}

function removeTask(id) {
  tasks.value = tasks.value.filter((task) => task.id !== id);
}

const editingId = ref(null);
const editingBuffer = ref('');

function startEdit(task) {
  editingId.value = task.id;
  editingBuffer.value = task.text;
}

function cancelEdit() {
  editingId.value = null;
  editingBuffer.value = '';
}

function finishEdit(task) {
  if (editingId.value !== task.id) return;

  const trimmed = editingBuffer.value.trim();

  if (!trimmed) removeTask(task.id);
  else task.text = trimmed;

  cancelEdit();
}

function toggleFav(task) {
  task.favorite = !task.favorite;
}

const search = ref('');
const activeFilter = ref('All');
const filters = ['All', 'Completed', 'Incomplete', 'Favorites'];

const filteredTasks = computed(() => {
  return tasks.value
    .filter((task) => task.text.toLowerCase().includes(search.value.toLowerCase()))
    .filter((task) => {
      if (activeFilter.value === 'All') return true;
      if (activeFilter.value === 'Completed') return task.completed;
      if (activeFilter.value === 'Incomplete') return !task.completed;
      if (activeFilter.value === 'Favorites') return task.favorite;
    });
});

watch(
  tasks,
  () => {
    localStorage.setItem('vue-todo-tasks', JSON.stringify(tasks.value));
  },
  { deep: true },
);

onMounted(() => {
  const saved = localStorage.getItem('vue-todo-tasks');
  if (!saved) return;

  try {
    tasks.value = JSON.parse(saved);
  } catch (e) {
    console.error('Failed to parse tasks from localStorage:', e);
  }
});
</script>

<template>
  <div class="wrapper">
    <h1>Todo App</h1>
  </div>

  <div class="input-row">
    <input type="text" placeholder="Add a new todo..." v-model="newTask" @keyup.enter="addTask" />

    <button @click="addTask">Add Todo</button>
  </div>

  <input type="text" placeholder="Search todos..." v-model="search" />

  <div class="filters">
    <button
      v-for="filter in filters"
      @click="activeFilter = filter"
      :key="filter"
      :class="{ active: activeFilter === filter }"
    >
      {{ filter }}
    </button>
  </div>

  <ul class="task-list">
    <li
      v-for="task in filteredTasks"
      :key="task.id"
      :class="{ done: task.completed, editing: editingId === task.id }"
    >
      <template v-if="editingId === task.id">
        <input
          type="text"
          v-model="editingBuffer"
          class="edit-input"
          @keyup.enter="finishEdit(task)"
          @keydown.esc="cancelEdit"
          @blur="finishEdit(task)"
          :ref="(el) => el && el.focus()"
        />
      </template>

      <template v-else>
        <button class="delete" @click="removeTask(task.id)">X</button>

        <button class="fav" @click="toggleFav(task)">
          {{ task.favorite ? '★' : '☆' }}
        </button>

        <input type="checkbox" v-model="task.completed" />
        <span @click="startEdit(task)">{{ task.text }}</span>
      </template>
    </li>
  </ul>
</template>

<style scoped>
.app {
  max-width: 500px;
  margin: 2rem auto;
  font-family: 'sans-serif';
  text-align: center;
}

.input-row {
  display: flex;
  gap: 0.5rem;
  margin-bottom: 1rem;
}

input {
  flex: 1;
  padding: 0.5rem;
  font-size: 1rem;
}

button {
  padding: 0.5rem 1rem;
  font-size: 1rem;
  cursor: pointer;
  border: 1px solid #ccc;
  cursor: pointer;
}

.task-list {
  list-style: none;
  padding: 0;

  & li {
    display: flex;
    align-items: center;
    gap: 0.5rem;
    padding: 0.5rem;
    border-bottom: 1px solid #eee;

    &.done span {
      text-decoration: line-through;
      opacity: 0.6;
    }
  }
}

.delete {
  background-color: #e53e3e;
  color: #fff;
  border: none;
  border-radius: 4px;
  padding: 0.2rem 0.5rem;
  cursor: pointer;

  &:hover {
    background-color: #c53030;
  }
}

.fav {
  background-color: none;
  border: none;
  cursor: pointer;
  font-size: 1.2rem;
  color: #f6c90e;

  &:hover {
    transform: scale(1.2);
  }
}

.filters {
  display: flex;
  flex-wrap: wrap;
  gap: 0.5rem;
  margin-bottom: 1rem;

  & button {
    padding: 0.3rem 0.8rem;
    border-radius: 6px;
    border: 1px solid #ccc;
    cursor: pointer;
    background-color: #f0f0f0;

    &.active {
      background-color: #333;
      color: #fff;
      border-color: #333;
    }
  }
}
</style>
