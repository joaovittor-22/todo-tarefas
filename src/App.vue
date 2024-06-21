<template>
  <div class="container">
    <div class="form-group">
      <input v-model="newTask" placeholder="Crie uma nova tarefa" class="form-control" required />
      <input type="checkbox" v-model="withDeadline" class="form-check-input" />
      <label class="form-check-label">Com prazo</label>
      <input type="datetime-local" v-if="withDeadline" v-model="deadline" class="form-control" />
      <button @click="addTodo" class="btn btn-primary rounded-btn">Salvar</button>
    </div>

    <div class="divider"></div>
    <ul class="todo-list">
      <ItemDaLista v-for="(todo, index) in todos" :key="index" :todo="todo" :index="index" :todos="todos" @update="updateTodo" @delete="deleteTodo" />
    </ul>
  </div>
</template>

<script setup>
import { ref, watch } from 'vue';
import ItemDaLista from './components/ItemDaLista.vue';

const newTask = ref('');
const withDeadline = ref(false);
const deadline = ref('');
const todos = ref(JSON.parse(localStorage.getItem('todos')) || []);

const addTodo = () => {
  if (newTask.value.trim() !== '') {
    todos.value.push({
      name: newTask.value,
      status: 'andamento',
      withDeadline: withDeadline.value,
      deadline: withDeadline.value ? deadline.value : null,
      isComplete: false,
      description: ''
    });
    newTask.value = '';
    withDeadline.value = false;
    deadline.value = '';
    saveTodos();
  }
};

const saveTodos = () => {
  localStorage.setItem('todos', JSON.stringify(todos.value));
};

const updateTodo = ({ index, todo }) => {
  todos.value[index] = todo;
  saveTodos();
};

const deleteTodo = (index) => {
  todos.value.splice(index, 1);
  saveTodos();
};

// Watch for changes to todos and save to local storage
watch(todos, (newTodos) => {
  localStorage.setItem('todos', JSON.stringify(newTodos));
}, { deep: true });
</script>
<style scoped>
.container {
  background: linear-gradient(to right, #006769, #00a99d);
  padding: 20px;
  border-radius: 10px;
  margin-bottom: 20px;
  min-height: 100vh; /* Garante que o container preencha a altura da viewport */
  display: flex;
  flex-direction: column;
}

.desc-title{
  font-family: Arial, Helvetica, sans-serif

}

.form-check-label{
  font-family: Arial, Helvetica, sans-serif;
 }


.form-group {
  margin-bottom: 20px;
}

.form-control {
  width: 60%; /* Largura limitada em telas normais */
  padding: 8px;
  margin-right: 10px;
}

.form-group input:first-child {
  border-radius: 12px;
}

.form-check-input {
  margin-right: 5px;
}

.btn {
  padding: 8px 20px;
  margin-left: 10px;
  cursor: pointer;

}

.btn:first-of-type {
  margin-top: 8px;
  border-radius: 6px;
}

.btn-primary {
  background-color: #007bff;
  border-color: #007bff;
  color: #fff;
}

.btn-secondary {
  background-color: #6c757d;
  border-color: #6c757d;
  color: #fff;
}


.todo-list {
  list-style: none;
  padding: 0;
  flex-grow: 1; /* Permite que a lista se expanda dentro do container */
  overflow-y: auto; /* Habilita rolagem se o conteúdo exceder a altura do container */
}


.divider {
  margin-top: 10px;
  margin-bottom: 10px;
  border: none;
  border-top: 1px solid #ddd;
}

/* Estilos do Select Personalizado */
@media (max-width: 1050px) {
  .form-control {
       margin-bottom:15px;
  }

}

@media (max-width: 480px) {
  .form-control {
    width: 100%; /* Largura ajustada para 100% para responsividade */
    padding: 8px;
    margin-right: 0; /* Removido margem direita para melhor espaçamento */
    box-sizing: border-box; /* Garante que padding e borda estão incluídos na largura */
    margin-bottom:15px;
  }

  .form-group {
    flex-direction: column;
    gap: 10px;
  }

  .btn {
    width: 80%;
  }

  .todo-item .card {
    display: flex;
    flex-direction: column;
    align-items: center;
  }

  /* .custom-select {
    width: 100%;
    margin-bottom: 10px;
  } */

  .options {
    width: 100%;
    left: 0;
    top: 100%;
  }

  .options.active {
    display: flex;
    flex-direction: column;
    align-items: center;
  }

  .description {
    font-size: 0.9em;
  }
}
</style>