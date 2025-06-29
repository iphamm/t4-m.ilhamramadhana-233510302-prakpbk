<script setup>
import { ref, onMounted } from 'vue';
import axios from 'axios';

const todos = ref([]);
const newTodo = ref('');
const loading = ref(false);
const error = ref('');

const editId = ref(null);
const editTitle = ref('');

const API_URL = 'http://localhost:3000/todos'; 

const loadTodos = async () => {
  loading.value = true;
  error.value = '';
  try {
    const response = await axios.get(API_URL);
    todos.value = response.data;
  } catch (err) {
    error.value = 'Gagal memuat data todo. Pastikan JSON Server berjalan di ' + API_URL;
    console.error('Error memuat todo:', err);
  } finally {
    loading.value = false;
  }
};

const addTodo = async () => {
  if (!newTodo.value.trim()) return;

  loading.value = true;
  error.value = '';
  try {
    const newData = {
      title: newTodo.value,
      completed: false,
    };
    const response = await axios.post(API_URL, newData);
    todos.value.push(response.data);
    newTodo.value = '';
  } catch (err) {
    error.value = 'Gagal menambah todo.';
    console.error('Error menambah todo:', err);
  } finally {
    loading.value = false;
  }
};

const toggleCompleted = async (todo) => {
  loading.value = true;
  error.value = '';
  try {
    const response = await axios.put(`${API_URL}/${todo.id}`, {
      ...todo,
      completed: !todo.completed,
    });
    const idx = todos.value.findIndex(t => t.id === todo.id);
    if (idx !== -1) todos.value[idx] = response.data;
  } catch (err) {
    error.value = 'Gagal memperbarui todo.';
    console.error('Error memperbarui todo:', err);
  } finally {
    loading.value = false;
  }
};

const deleteTodo = async (id) => {
  loading.value = true;
  error.value = '';
  try {
    await axios.delete(`${API_URL}/${id}`);
    todos.value = todos.value.filter(todo => todo.id !== id);
  } catch (err) {
    error.value = 'Gagal menghapus todo.';
    console.error('Error menghapus todo:', err);
  } finally {
    loading.value = false;
  }
};

const startEdit = (todo) => {
  editId.value = todo.id;
  editTitle.value = todo.title;
};

const cancelEdit = () => {
  editId.value = null;
  editTitle.value = '';
};

const updateTodo = async (todo) => {
  if (!editTitle.value.trim()) return;

  loading.value = true;
  error.value = '';
  try {
    const response = await axios.put(`${API_URL}/${todo.id}`, {
      ...todo,
      title: editTitle.value,
    });
    const idx = todos.value.findIndex(t => t.id === todo.id);
    if (idx !== -1) todos.value[idx] = response.data;
    editId.value = null;
    editTitle.value = '';
  } catch (err) {
    error.value = 'Gagal memperbarui todo.';
    console.error('Error memperbarui todo:', err);
  } finally {
    loading.value = false;
  }
};

onMounted(loadTodos);
</script>

<template>
  <div class="todo-app-container">
    <h1 class="app-title">Todo List dengan Axios & JSON Server (CRUD lengkap)</h1>

    <form @submit.prevent="addTodo" class="add-todo-form">
      <input v-model="newTodo" placeholder="Tambah todo baru..." required class="add-todo-input" />
      <button type="submit" :disabled="loading" class="add-todo-button">Tambah</button>
    </form>

    <p v-if="loading" class="message loading-message">Memuat data...</p>
    <p v-if="error" class="message error-message">{{ error }}</p>

    <ul v-if="todos.length" class="todo-list">
      <li v-for="todo in todos" :key="todo.id" class="todo-item">
        <label class="todo-checkbox-container">
          <input
            type="checkbox"
            :checked="todo.completed"
            @change="toggleCompleted(todo)"
            class="todo-checkbox"
          />
          <span class="checkmark"></span>
        </label>

        <template v-if="editId !== todo.id">
          <span :class="{ 'completed-todo': todo.completed }" class="todo-text">
            {{ todo.title }}
          </span>
          <button @click="startEdit(todo)" class="button edit-button">Edit</button>
        </template>

        <template v-else>
          <input v-model="editTitle" class="edit-todo-input" @keyup.enter="updateTodo(todo)" @keyup.esc="cancelEdit" />
          <button @click="updateTodo(todo)" class="button save-button">Simpan</button>
          <button @click="cancelEdit" class="button cancel-button">Batal</button>
        </template>

        <button @click="deleteTodo(todo.id)" class="button delete-button">Hapus</button>
      </li>
    </ul>

    <p v-else class="message no-todos-message">Tidak ada todo.</p>
  </div>
</template>

<style scoped>
.todo-app-container {
  max-width: 620px; 
  margin: 60px auto;
  padding: 50px; 
  background-color: var(--card-background);
  border-radius: var(--border-radius-large);
  box-shadow: var(--box-shadow-base);
  box-sizing: border-box;
  color: var(--text-dark);
}

.app-title {
  text-align: center;
  color: var(--primary-color);
  margin-bottom: 45px;
  font-size: 2.6em; 
  font-weight: 700;
  letter-spacing: -0.8px; 
  text-shadow: 1px 1px 2px rgba(0, 0, 0, 0.05); 
}

.add-todo-form {
  display: flex;
  gap: 15px; 
  margin-bottom: 35px;
  align-items: stretch;
}

.add-todo-input {
  flex-grow: 1;
  padding: 15px 20px; 
  border: 1px solid var(--border-color);
  border-radius: var(--border-radius-medium);
  font-size: 1.1em; 
  color: var(--text-dark);
  background-color: var(--card-background);
  transition: border-color 0.3s ease, box-shadow 0.3s ease;
}

.add-todo-input::placeholder {
  color: var(--text-medium);
  opacity: 0.8; 
}

.add-todo-input:focus {
  outline: none;
  border-color: var(--input-focus-border);
  box-shadow: 0 0 0 4px rgba(3, 169, 244, 0.2); 
}

.add-todo-button {
  padding: 15px 30px; 
  background-color: var(--primary-color);
  color: white;
  border: none;
  border-radius: var(--border-radius-medium);
  cursor: pointer;
  font-size: 1.1em;
  font-weight: 600;
  transition: background-color 0.3s ease, transform 0.2s ease, box-shadow 0.3s ease;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1); 
}

.add-todo-button:hover:not(:disabled) {
  background-color: var(--primary-dark);
  transform: translateY(-3px); 
  box-shadow: var(--box-shadow-hover); 
}

.add-todo-button:active:not(:disabled) {
  transform: translateY(0); 
  box-shadow: 0 1px 5px rgba(0, 0, 0, 0.1);
}

.add-todo-button:disabled {
  background-color: #e0e0e0; 
  color: #a0a0a0;
  cursor: not-allowed;
  opacity: 0.9;
  box-shadow: none;
}

.message {
  text-align: center;
  padding: 18px; 
  border-radius: var(--border-radius-medium);
  margin-top: 30px; 
  font-size: 1em;
  background-color: #f8f9fa; 
  border: 1px solid #e9ecef;
  color: var(--text-medium);
  box-shadow: 0 1px 5px rgba(0, 0, 0, 0.05); 
}

.loading-message {
  background-color: #e3f2fd;
  border-color: #bbdefb;
  color: var(--secondary-dark); 
  font-weight: 500;
}

.error-message {
  background-color: #ffebee;
  border-color: #ffcdd2;
  color: var(--danger-dark); 
  font-weight: 600; 
}

.no-todos-message {
  background-color: #fdfdfd;
  border-color: #e0e0e0;
  color: var(--text-light);
  font-style: italic;
}

.todo-list {
  list-style: none;
  padding: 0;
  margin: 0;
}

.todo-item {
  display: flex;
  align-items: center;
  background-color: var(--card-background);
  padding: 20px 25px; 
  border-radius: var(--border-radius-medium);
  margin-bottom: 18px; 
  box-shadow: 0 3px 12px rgba(0, 0, 0, 0.07); 
  transition: transform 0.2s ease, box-shadow 0.2s ease, border-color 0.3s ease;
  position: relative;
  border: 1px solid var(--border-color);
}

.todo-item:hover {
  transform: translateY(-4px); 
  box-shadow: var(--box-shadow-hover); 
  border-color: var(--secondary-color); 
}

.todo-checkbox-container {
  display: block;
  position: relative;
  padding-left: 32px; 
  margin-right: 18px;
  cursor: pointer;
  user-select: none;
  min-width: 24px; 
}

.todo-checkbox-container input[type="checkbox"] {
  position: absolute;
  opacity: 0;
  cursor: pointer;
  height: 0;
  width: 0;
}

.checkmark {
  position: absolute;
  top: 0;
  left: 0;
  height: 24px; 
  width: 24px;
  background-color: #f0f0f0; 
  border: 2px solid var(--border-color);
  border-radius: 6px; 
  transition: background-color 0.3s, border-color 0.3s, box-shadow 0.2s;
}

.todo-checkbox-container:hover input ~ .checkmark {
  background-color: #e5e5e5;
  border-color: var(--secondary-color); 
}

.todo-checkbox-container input:checked ~ .checkmark {
  background-color: var(--primary-color);
  border-color: var(--primary-color);
  box-shadow: 0 0 0 3px rgba(92, 184, 92, 0.3); 
}

.checkmark:after {
  content: "";
  position: absolute;
  display: none;
  left: 8px; 
  top: 4px;
  width: 6px; 
  height: 12px; 
  border: solid white;
  border-width: 0 3px 3px 0;
  transform: rotate(45deg);
}

.todo-checkbox-container input:checked ~ .checkmark:after {
  display: block;
}

.todo-text {
  flex-grow: 1;
  font-size: 1.15em; 
  color: var(--text-dark);
  word-break: break-word;
  line-height: 1.5; 
  margin-right: 15px;
}

.completed-todo {
  text-decoration: line-through;
  color: var(--text-light);
  font-style: italic;
  opacity: 0.8; 
}

.edit-todo-input {
  flex-grow: 1;
  padding: 12px 15px;
  border: 1px solid var(--secondary-color);
  border-radius: var(--border-radius-medium);
  font-size: 1.1em;
  color: var(--text-dark);
  background-color: var(--card-background);
  margin-right: 15px;
  transition: border-color 0.3s ease, box-shadow 0.3s ease;
}

.edit-todo-input:focus {
  outline: none;
  box-shadow: 0 0 0 4px rgba(3, 169, 244, 0.2);
}

.button {
  padding: 12px 20px; 
  border: none;
  border-radius: var(--border-radius-small);
  cursor: pointer;
  font-size: 0.98em; 
  font-weight: 500;
  margin-left: 10px; 
  transition: background-color 0.3s ease, transform 0.2s ease, box-shadow 0.3s ease;
  white-space: nowrap;
  color: white;
  box-shadow: 0 2px 6px rgba(0, 0, 0, 0.08); 
}

.button:hover {
  transform: translateY(-2px); 
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.12); 
}

.button:active {
  transform: translateY(0);
  box-shadow: 0 1px 4px rgba(0, 0, 0, 0.08);
}

.edit-button {
  background-color: var(--secondary-color);
}
.edit-button:hover {
  background-color: var(--secondary-dark);
}

.save-button {
  background-color: var(--primary-color);
}
.save-button:hover {
  background-color: var(--primary-dark);
}

.cancel-button {
  background-color: var(--info-color);
}
.cancel-button:hover {
  background-color: var(--info-dark);
}

.delete-button {
  background-color: var(--danger-color);
}
.delete-button:hover {
  background-color: var(--danger-dark);
}

@media (max-width: 650px) { 
  .todo-app-container {
    margin: 30px 15px; 
    padding: 30px;
  }

  .app-title {
    font-size: 2em;
    margin-bottom: 30px;
  }

  .add-todo-form {
    flex-direction: column;
    gap: 12px;
  }

  .add-todo-button {
    width: 100%;
  }

  .todo-item {
    flex-wrap: wrap;
    padding: 15px 20px;
  }

  .todo-text {
    flex-basis: 100%;
    order: 1;
    margin-bottom: 12px;
    margin-right: 0;
  }

  .todo-checkbox-container {
    order: 0;
    margin-right: 10px;
  }

  .button {
    margin-left: 0;
    margin-right: 8px; 
    margin-top: 8px;
    flex-basis: calc(50% - 8px);
    padding: 10px 15px;
  }

  .edit-todo-input {
    flex-basis: 100%;
    margin-right: 0;
    margin-bottom: 12px;
  }
}

@media (max-width: 450px) { 
  .button {
    flex-basis: 100%;
    margin-right: 0;
    margin-left: 0;
  }
}
</style>