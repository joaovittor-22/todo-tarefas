<template>
  <div>
    <div  class="info-section">
      <div v-if="todo.status !== 'cancelado'">
           <div v-if="todo.withDeadline" class="deadline-info">
           <div class="deadline-details">
             <div>
               <strong>Prazo:</strong> {{ formatDeadline(todo.deadline) }}
             </div>
             <div>
               <strong>Status:</strong> {{ getDeadlineStatus(todo.deadline) }}
             </div>
           </div>
         </div>
      </div>
      <div v-else class="cancelled">
        <div>
        <strong>Status:</strong> cancelado
      </div>
     </div>
     <div v-if="todo.description" class="description">
        <strong>Descrição:</strong> {{ todo.description }}
      </div>
    </div>
  </div>
</template>

<script setup>
import { defineProps } from 'vue';

const props = defineProps({
  todo: Object
});

// Explicitly use props.todo to inform the linter that it is indeed used
console.log(props.todo);

const getDeadlineStatus = (date) => {
  const today = new Date();
  const dueDate = new Date(date);

  if ( today > dueDate && props.todo.status === 'andamento') { //passou da data de entrega mas andamento
    return 'atrasado';
  } else if (dueDate.toDateString() === today.toDateString() &&  props.todo.status === 'concluido') { 
    return 'concluido';
  }
  else if(today < dueDate &&  props.todo.status === 'concluido') { //concluiu antes da data de entrega 
    return 'concluido (adiantado)';
  }
  else if(today > dueDate && props.todo.status === 'concluido'){ //concluiu depois da data de entrega - a data atual esta acima da data que deveria ter entregue
    return 'Concluido (atrasado)';
  }
  else if( today <= dueDate && props.todo.status === 'andamento' ) { //data atual menor ou igual que a data de entrega 
    return 'em dia';
  }
};

const formatDeadline = (date) => {
  const dueDate = new Date(date);
  return dueDate.toLocaleString('pt-BR', { hour: '2-digit', minute: '2-digit', day: '2-digit', month: '2-digit', year: 'numeric' });
};
</script>

<style scoped>
.info-section {
  margin-top: 10px;
}

.deadline-info {
  background-color: #f0f0f0;
  padding: 10px;
  border-radius: 8px;
  margin-bottom: 10px;
}

.deadline-info {
  background-color: #f0f0f0;
  padding: 10px;
  border-radius: 8px;
  margin-bottom: 10px;
}


.description {
  margin-top: 5px;
  font-size: 0.9em;
  color: #555;
}

.cancelled {
  /* background-color: #f8d7da; */
  color: #721c24;
  padding: 10px;
  border-radius: 8px;
  margin-bottom: 10px;
}

.cancelled strong {
  font-weight: bold;
  font-size: 1.1em;
}
</style>


