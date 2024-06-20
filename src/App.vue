<template>
  <div class="container">
    <div class="form-group">
      <input v-model="newTask" placeholder="Crie uma nova tarefa" class="form-control" required/>
      <input type="checkbox" v-model="withDeadline" class="form-check-input" />
      <label class="form-check-label">Com prazo</label>
      <input type="datetime-local" v-if="withDeadline" v-model="deadline" class="form-control" /> 
      <button @click="addTodo" class="btn btn-primary rounded-btn">Salvar</button>
    </div>
    <div class="divider"></div> <!-- Espaço entre a seção de adicionar item e a lista -->
    <ul class="todo-list">
      <div v-for="(todo, index) in todos" :key="index" class="todo-item" :class="{ 'completed': todo.isComplete }">
        <div v-if="!todo.isComplete" class="card">
          <input v-model="todo.name" placeholder="Tarefa" @input="saveTodos" class="form-control" />
          <div class="custom-select">
            <select v-model="todo.status" @change="saveTodos"
                    :style="{ borderRightColor: getBorderColor(todo.status) }">
              <option value="concluido">Concluído</option>
              <option value="andamento">Em andamento</option>
              <option value="cancelado">Cancelado</option>
            </select>
            <div class="options" v-if="showOptions">
              <div v-for="option in statusOptions" :key="option.value"
                   class="option" @click="selectOption(option)">
                {{ option.label }}
              </div>
            </div>
          </div>
          <button @click="openModal(todo)" class="btn btn-secondary rounded-btn">Adicionar Descrição</button> <!-- Adicionar descrição -->
          <button @click="deleteTodo(index)" class="btn btn-danger rounded-btn">Excluir</button> <!-- Botão de exclusão -->


      <div v-if="todo.status !== 'cancelado'" class="info-section">
        <div v-if="todo.withDeadline" class="deadline-info">
          <div>
            Prazo: {{ formatDeadline(todo.deadline) }}
          </div>
          <div>
            Status: {{ getDeadlineStatus(todo.deadline) }}
          </div>
          <div v-if="todo.description" class="description">
            Descrição: {{ todo.description }}
          </div>
        </div>
      </div>
      <div v-else class="cancelled">
        <div>
          Status: cancelado
        </div>
        <div v-if="todo.description" class="description">
          Descrição: {{ todo.description }}
        </div>
      </div>
      <div v-if="!todo.withDeadline && todo.status !== 'cancelado'" class="info-section">
        <div v-if="todo.description" class="description">
          Descrição: {{ todo.description }}
        </div>
      </div>
    </div>
  </div>
</ul>
<div v-if="modalOpen" class="modal">
  <div class="modal-content">
    <span class="close" @click="closeModal">&times;</span>
    <h2 class="desc-title">Adicione uma Descrição</h2>
    <textarea v-model="modalDescription" class="form-control"></textarea>
    <button @click="saveDescription" class="btn btn-primary rounded-btn">Salvar</button>
  </div>
</div>

  </div>
</template>
<script setup>
import { ref, watch } from 'vue'

const newTask = ref('')
const withDeadline = ref(false)
const deadline = ref('')
const todos = ref(JSON.parse(localStorage.getItem('todos')) || [])
const modalOpen = ref(false)
const modalDescription = ref('')
const currentTodo = ref(null)
const showOptions = ref(false)
const statusOptions = [
  { value: 'concluido', label: 'Concluído', color: '#28a745' },
  { value: 'andamento', label: 'Em andamento', color: '#ffc107' },
  { value: 'cancelado', label: 'Cancelado', color: '#dc3545' }
]

const addTodo = () => {
  if (newTask.value.trim() !== '') {
    todos.value.push({
      name: newTask.value,
      status: 'andamento',
      withDeadline: withDeadline.value,
      deadline: withDeadline.value ? deadline.value : null,
      isComplete: false,
      description: ''
    })
    newTask.value = ''
    withDeadline.value = false
    deadline.value = ''
    saveTodos()
  }
}

const saveTodos = () => {
  localStorage.setItem('todos', JSON.stringify(todos.value))
}

const deleteTodo = (index) => {
  todos.value.splice(index, 1)
  saveTodos()
}

const getDeadlineStatus = (date) => {
  const today = new Date()
  const dueDate = new Date(date)
  if (dueDate < today) {
    return 'atrasado'
  } else if (dueDate.toDateString() === today.toDateString()) {
    return 'em dias'
  } else {
    return 'adiantado'
  }
}

const formatDeadline = (date) => {
  const dueDate = new Date(date)
  return dueDate.toLocaleString('pt-BR', { hour: '2-digit', minute: '2-digit', day: '2-digit', month: '2-digit', year: 'numeric' })
}

const openModal = (todo) => {
  modalDescription.value = todo.description || ''
  currentTodo.value = todo
  modalOpen.value = true
}

const closeModal = () => {
  modalOpen.value = false
  currentTodo.value = null
}

const saveDescription = () => {
  if (currentTodo.value) {
    currentTodo.value.description = modalDescription.value
    saveTodos()
    closeModal()
  }
}

// Escuta as mudas na lista de tasks a serem salvas
watch(todos, (newTodos) => {
  localStorage.setItem('todos', JSON.stringify(newTodos))
}, { deep: true })

const selectOption = (option) => {
  currentTodo.value.status = option.value
  saveTodos()
  showOptions.value = false
}

const getBorderColor = (status) => {
  const statusColors = {
    'concluido': '#28a745',   // Verde para 'concluido'
    'andamento': '#ffc107',   // Amarelo para 'andamento'
    'cancelado': '#dc3545'    // Vermelho para 'cancelado'
  };
  return statusColors[status] || '#ccc'; // Retorna cinza como padrão se o status não for encontrado
}
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

@media (max-width: 1245px) and (min-width: 481px) {
  .btn-danger{
    margin-top: 15px;
  }
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

.btn-danger {
  background-color: #dc3545;
  border-color: #dc3545;
  color: #fff;
}

.rounded-btn {
  border-radius: 12px;
}

.todo-list {
  list-style: none;
  padding: 0;
  flex-grow: 1; /* Permite que a lista se expanda dentro do container */
  overflow-y: auto; /* Habilita rolagem se o conteúdo exceder a altura do container */
}

.todo-item {
  margin-bottom: 10px;
  min-height: 80px;
  background-color: rgba(255, 255, 255, 0.8);
  border-radius: 8px;
  padding: 10px;
}

.todo-item.completed {
  opacity: 0.6;
}

.card {
  padding: 10px;
  border-radius: 8px;
  background-color: rgba(255, 255, 255, 0.8);
}

.info-section {
  margin-top: 5px;
}

.deadline-info {
  margin-bottom: 5px;
}

.cancelled {
  margin-top: 5px;
  color: #dc3545;
}

.description {
  margin-top: 5px;
  font-size: 0.9em;
}

.modal {
  display: block;
  position: fixed;
  z-index: 1;
  left: 0;
  top: 0;
  width: 100%;
  height: 100%;
  overflow: auto;
  background-color: rgba(0, 0, 0, 0.4);
}

.modal-content {
  background-color: #fefefe;
  margin: 15% auto;
  padding: 20px;
  border: 1px solid #888;
  width: 80%;
}

.close {
  color: #aaa;
  float: right;
  font-size: 28px;
  font-weight: bold;
}

.close:hover,
.close:focus {
  color: black;
  text-decoration: none;
  cursor: pointer;
}

.divider {
  margin-top: 10px;
  margin-bottom: 10px;
  border: none;
  border-top: 1px solid #ddd;
}

/* Estilos do Select Personalizado */
.custom-select {
  position: relative;
  display: inline-block;
  width: 200px; /* Ajuste a largura conforme necessário */
}

.custom-select select {
  display: block;
  width: 100%;
  padding: 8px;
  cursor: pointer;
  appearance: none;
  -webkit-appearance: none;
  border: 1px solid #ccc;
  border-radius: 4px;
  background-color: #fff;
  color: #000;
  border-right: #ccc 8px solid;
}

.custom-select .options {
  position: absolute;
  z-index: 1;
  top: 100%;
  left: 0;
  width: 100%;
  display: none;
  background-color: #fff;
  border: 1px solid #ccc;
  border-top: none;
  border-radius: 0 0 4px 4px;
}

.custom-select .options.active {
  display: block;
}

.custom-select .option {
  padding: 8px;
  cursor: pointer;
}

.custom-select .option:hover {
  background-color: #f0f0f0;
}

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

  .custom-select {
    width: 100%;
    margin-bottom: 10px;
  }

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

  .todo-item .card button {
    width: 100%;
    margin-top: 10px;
  }

  .description {
    font-size: 0.9em;
  }
}
</style>
