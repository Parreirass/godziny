<template>
  <div class="card">
    <ul class="list-group list-group-flush" v-if="selecao === '1'">
      <li class="list-group-item"><strong>Título:</strong> {{ atividade.titulo }}</li>
      <li class="list-group-item"><strong>Categoria:</strong> {{ atividade.categoria.nome }}</li>
      <li class="list-group-item"><strong>Arquivo:</strong> {{ fileName }}</li>
      <li class="list-group-item"><strong>Data e Hora:</strong> {{ formattedDataHora }}</li>
      <li class="list-group-item"><strong>Status:</strong> {{ atividade.status }}</li>
    </ul>
    <ul class="list-group list-group-flush" v-if="selecao === '2'">
      <li class="list-group-item"><strong>Nome:</strong> {{ categoria.nome }}</li>
      <li class="list-group-item"><strong>Curso:</strong> {{ categoria.curso.nome }}</li>
      <li class="list-group-item"><strong>Carga Horária:</strong> {{ categoria.curso.carga_horaria_complementar }}</li>
      <li class="list-group-item"><strong>Procentagem de horas máximas:</strong> {{ categoria.porcentagemHorasMaximas }}</li>
    </ul>
    <ul class="list-group list-group-flush" v-if="selecao === '3'">
      <li class="list-group-item"><strong>Nome:</strong> {{ curso.nome }}</li>
      <li class="list-group-item"><strong>Curso:</strong> {{ curso.coordenador.nome }}</li>
      <li class="list-group-item"><strong>Carga Horária:</strong> {{ curso.carga_horaria_complementar }}</li>
    </ul>
    <ul class="list-group list-group-flush" v-if="selecao === '4'">
      <li class="list-group-item"><strong>Nome:</strong> {{ usuario.nome }}</li>
      <li class="list-group-item"><strong>Curso:</strong> {{ usuario.curso.nome }}</li>
      <li class="list-group-item"><strong>Matricula:</strong> {{ usuario.matricula}}</li>
      <li class="list-group-item"><strong>Email:</strong> {{ usuario.email }}</li>
    </ul>
  </div>
</template>

<script setup>
import { defineProps, computed} from 'vue';

// Recebendo as props do componente pai
const props = defineProps({
  selecao: {
    type: String,
    required: false
  },
  atividade: {
    type: Object,
    required: false
  },
  categoria: {
    type: Object,
    required: false
  },
  curso: {
    type: Object,
    required: false
  },
  usuario: {
    type: Object,
    required: false
  }
});
// Computed para extrair o nome do arquivo
const fileName = computed(() => {
  return props.atividade.file ? props.atividade.file.name : 'Nenhum arquivo selecionado';
});

// Computed para formatar a data e hora
const formattedDataHora = computed(() => {
  if (!props.atividade.createdAt) return 'Data e hora não especificadas';
  const date = new Date(props.atividade.createdAt);
  return date.toLocaleString(); // Formata a data e hora conforme as configurações locais
});

</script>

<style scoped>
.card {
  margin: 1rem;
}

.list-group-item {
  font-size: 1rem;
}
</style>
