<!-- ---------------------------------------------------------------------- -->
<!-- HTML                                                                   -->
<!-- ---------------------------------------------------------------------- -->
<template>
  <div class="container py-4">
      <div class="row">
          <div class="col">
              <h2 class="text-secondary">Nova Categoria</h2>
          </div>
          <form @submit.prevent="adicionarCategoria">
              <div class="h-100 p-5 bg-light border rounded-3">
                  <div class="mb-3">
                      <label for="nome" class="form-label">Nome</label>
                      <div class="input-group">
                          <input type="text" class="form-control" id="nome"
                          v-model="nome" @input="formatarNome"
                          aria-describedby="basic-addon3 basic-addon4" required
                          minlength="3" maxlength="250">
                      </div>
                      <small v-if="nomeError" class="text-danger">{{ nomeError }}</small>
                  </div>
                  <div class="mb-3">
                      <label for="descricao" class="form-label">Descrição</label>
                      <div class="input-group">
                          <textarea class="form-control" id="descricao"
                          v-model="descricao" @input="formatarDescricao"
                          aria-label="With textarea" required
                          minlength="3" maxlength="500"></textarea>
                      </div>
                      <small v-if="descricaoError" class="text-danger">{{ descricaoError }}</small>
                  </div>
                  <div class="mb-3">
                      <label for="max-horas" class="form-label">Percentual Máximo de horas</label>
                      <div class="input-group">
                          <input type="number" class="form-control" id="max-horas"
                          v-model.number="maxHoras" required
                          min="1" max="100">
                      </div>
                      <small v-if="maxHorasError" class="text-danger">{{ maxHorasError }}</small>
                  </div>
                  <div class="mb-3">
                      <label for="multiplicador" class="form-label">Multiplicador de Horas</label>
                      <div class="input-group">
                          <input type="number" class="form-control" id="multiplicador"
                          v-model.number="multiplicador" min="0.1" step="0.1" value="1.0">
                      </div>
                      <small v-if="multiplicadorError" class="text-danger">{{ multiplicadorError }}</small>
                  </div>
                  <div class="row">
                      <div class="col d-flex justify-content-end">
                          <button type="submit" class="btn btn-primary text-white">Adicionar</button>
                      </div>
                  </div>
              </div>
          </form>
      </div>
  </div>
</template>



<!-- ---------------------------------------------------------------------- -->
<!-- JavaScript                                                             -->
<!-- ---------------------------------------------------------------------- -->
<script setup>
import { ref, watch } from 'vue';

const nome = ref('');
const descricao = ref('');
const maxHoras = ref(null);
const multiplicador = ref(1.0);

const nomeError = ref('');
const descricaoError = ref('');
const maxHorasError = ref('');
const multiplicadorError = ref('');

const formatarNome = () => {
    // Exemplo de formatação, se necessário
    nome.value = nome.value.trim();
};

const formatarDescricao = () => {
    // Exemplo de formatação, se necessário
    descricao.value = descricao.value.trim();
};

watch(nome, (newValue) => {
    if (newValue.length < 3 || newValue.length > 250) {
      setTimeout(() => {
        nomeError.value = 'O nome deve ter entre 3 e 250 caracteres.';
        }, 3000);
    } else {
      setTimeout(() => {
            nomeError.value = '';
        }, 3000);
    }
});

watch(descricao, (newValue) => {
    if (newValue.length < 3 || newValue.length > 500) {
      setTimeout(() => {
        descricaoError.value = 'A descrição deve ter entre 3 e 500 caracteres.';
      }, 3000);
    } else {
      setTimeout(() => {
        descricaoError.value = '';
      }, 3000);
    }
});

watch(maxHoras, (newValue) => {
    if (newValue < 1 || newValue > 100) {
      setTimeout(() => {
        maxHorasError.value = 'O percentual máximo de horas deve ser entre 1 e 100.';
      }, 3000);
    } else {
      setTimeout(() => {
        maxHorasError.value = '';
      }, 3000);
    }
});

watch(multiplicador, (newValue) => {
    if (newValue < 0.1) {
      setTimeout(() => {
        multiplicadorError.value = 'O multiplicador deve ser pelo menos 0.1.';
      }, 3000);
    } else {
      setTimeout(() => {
        multiplicadorError.value = '';
      }, 3000);
    }
});

const adicionarCategoria = () => {
    // Adicione a lógica para enviar os dados ou processar a adição da categoria
    console.log('Categoria adicionada:', { nome: nome.value, descricao: descricao.value, maxHoras: maxHoras.value, multiplicador: multiplicador.value });
};

</script>


<!-- ---------------------------------------------------------------------- -->
<!-- CSS                                                                    -->
<!-- ---------------------------------------------------------------------- -->
<style scoped>
* {
  color: #464545;
}
</style>