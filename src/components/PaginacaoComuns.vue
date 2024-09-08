<!-- ---------------------------------------------------------------------- -->
<!-- HTML                                                                   -->
<!-- ---------------------------------------------------------------------- -->
<template>
  <ul class="pagination">
    <li class="page-item">
      <a class="page-link" href="#" aria-label="Previous" @click.prevent="previous">
        <span aria-hidden="true">&laquo;</span>
      </a>
    </li>
    <li class="page-item" v-for="(pagina, index) in vetorTotalPaginas" :key="index">
      <a class="page-link" href="#" @click.prevent="mudarPagina(pagina)">{{ pagina }}</a>
    </li>
    <li class="page-item">
      <a class="page-link" href="#" aria-label="Next" @click.prevent="next">
        <span aria-hidden="true">&raquo;</span>
      </a>
    </li>
  </ul>
</template>

<!-- ---------------------------------------------------------------------- -->
<!-- JavaScript                                                             -->
<!-- ---------------------------------------------------------------------- -->
<script setup>
import { defineProps, computed, defineEmits, ref } from 'vue';
// Definindo as props
const props = defineProps({
  totalPaginas: {
    type: Number,
    required: false,
    default: 1 // Valor padrão se totalPaginas não for fornecido
  },
  paginaAtual:{
    type: Number,
    required: false,
    default: 0
  }
});
const paginaAtual = ref(props.paginaAtual)

// Computed para gerar o vetor de páginas
const vetorTotalPaginas = computed(() => {
  let vet = [];
  for (let i = 1; i <= props.totalPaginas; i++) {
    vet.push(i); // Adiciona o número da página ao vetor
  }
  return vet;
});

// Definindo os eventos que o componente pode emitir
const emit = defineEmits(['valorAtualizado']);

// Função para avançar para a próxima página
const next = () => {
  if (paginaAtual.value >= 0 && paginaAtual.value < props.totalPaginas - 1) {
    paginaAtual.value += 1;
    emitirValor();
  }
};

// Função para voltar para a página anterior
const previous = () => {
  if (paginaAtual.value > 0 && paginaAtual.value <= props.totalPaginas) {
    paginaAtual.value -= 1;
    emitirValor();
  }
};

// Função para mudar para uma página específica
const mudarPagina = (pagina) => {
  paginaAtual.value = pagina - 1;
  emitirValor();
};

// Função para emitir o valor atualizado da página atual
const emitirValor = () => {
  emit('valorAtualizado', paginaAtual.value);
};

</script>

<!-- ---------------------------------------------------------------------- -->
<!-- CSS                                                                    -->
<!-- ---------------------------------------------------------------------- -->
<style scoped></style>
