<template>
  <div v-if="!todo.isComplete" class="card">
    <div class="input-wrapper">
      <input v-model="localTodo.name" placeholder="Tarefa" @input="updateTodoName" class="form-control" />
      <div class="custom-select">
        <select v-model="localTodo.status" @change="updateTodoStatus" :style="{ borderRightColor: getBorderColor(localTodo.status) }">
          <option value="concluido">Concluído</option>
          <option value="andamento">Em andamento</option>
          <option value="cancelado">Cancelado</option>
        </select>
      </div>
    </div>
    <button @click="openModal" class="btn btn-secondary rounded-btn">Adicionar Descrição</button>
    <button @click="deleteTodo" class="btn btn-danger rounded-btn">Excluir</button>
    
    <InfoSection :todo="todo"/>

    <!-- Modal Section -->
    <div v-if="modalOpen" class="modal">
      <div class="modal-content">
        <span class="close" @click="closeModal">&times;</span>
        <h2 class="desc-title">Adicione uma Descrição</h2>
        <textarea v-model="modalDescription" class="form-control"></textarea>
        <button @click="saveDescription" class="btn btn-primary rounded-btn">Salvar</button>
      </div>
    </div>
    <!-- End Modal Section -->
  </div>
</template>

<script setup>
import { ref, defineProps, defineEmits, watch } from 'vue';
import InfoSection from './InfoSection.vue';

const props = defineProps({
  todo: Object,
  index: Number,
  todos: Array
});

const emits = defineEmits(['update', 'delete']);

const localTodo = ref({ ...props.todo });
const modalOpen = ref(false);
const modalDescription = ref(localTodo.value.description || '');

watch(() => props.todo, (newVal) => {
  localTodo.value = { ...newVal };
  modalDescription.value = newVal.description || '';
});

const deleteTodo = () => {
  emits('delete', props.index);
};

const openModal = () => {
  modalOpen.value = true;
};

const closeModal = () => {
  modalOpen.value = false;
};

const saveDescription = () => {
  localTodo.value.description = modalDescription.value;
  updateTodo();
  closeModal();
};

const updateTodoName = () => {
  updateTodo();
};

const updateTodoStatus = () => {
  updateTodo();
};

const updateTodo = () => {
  emits('update', { index: props.index, todo: localTodo.value });
};

const getBorderColor = (status) => {
  const statusColors = {
    'concluido': '#28a745',
    'andamento': '#ffc107',
    'cancelado': '#dc3545'
  };
  return statusColors[status] || '#ccc';
};
</script>

  <style scoped>
  .card {
    padding: 10px;
    border-radius: 8px;
    background-color: rgba(255, 255, 255, 0.8);
    margin-bottom: 25px;
  }
  
  .input-wrapper {
    display: flex;
    align-items: center;
  }
  
  .form-control {
    width: 30%; /* Full width by default */
    padding: 10px;
    margin-bottom: 10px;
    border: 1px solid #ccc;
    border-radius: 4px;
    box-shadow: inset 0 1px 3px rgba(0, 0, 0, 0.1);
  }

  .form-control:focus {
    outline: none;
}
  
  .custom-select {
    margin-left: 10px; /* Spacing between input and select */
  }
  
  .btn {
    padding: 8px 20px;
    margin-left: 10px;
    cursor: pointer;
  }
  
  .btn-danger {
    background-color: #dc3545;
    border-color: #dc3545;
    color: #fff;
  }
  
  .rounded-btn {
    border-radius: 12px;
  }
  
  .custom-select {
    position: relative;
    display: inline-block;
    width: 200px; /* Adjust width as necessary */
    margin-bottom: 10px;
  }
  
  .custom-select select {
    display: block;
    padding: 10px;
    cursor: pointer;
    appearance: none;
    -webkit-appearance: none;
    border: 1px solid #ccc;
    border-radius: 4px;
    background-color: #fff;
    color: #000;
    border-right: 10px solid #ccc;
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

  @media (max-width: 768px) {
    .input-wrapper {
      flex-direction: column;
      align-items: flex-start;
    }
  
    .custom-select {
      width: 100%;
      margin-top: 10px;
    }
  
    .form-control {
      width: 90%;
      margin-left: auto;
      margin-right: auto;
    }
  }
  </style>
  