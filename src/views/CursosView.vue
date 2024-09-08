<!-- ---------------------------------------------------------------------- -->
<!-- HTML                                                                   -->
<!-- ---------------------------------------------------------------------- -->
<template>
    <div class="container py-4">
        <div class="row">
            <div class="col">
                <h2 class="text-secondary">Novo curso</h2>
            </div>
            <form @submit.prevent="adicionarCurso">
                <div class="h-100 p-5 bg-light border rounded-3">
                    <div class="mb-3">
                        <label for="sigla" class="form-label">Sigla do Curso</label>
                        <div class="input-group">
                            <input type="text" class="form-control" id="sigla" v-model="sigla" @input="formatarSigla"
                                aria-describedby="basic-addon3 basic-addon4" required minlength="3" maxlength="20">
                        </div>
                        <small v-if="siglaError" class="text-danger">{{ siglaError }}</small>
                    </div>
                    <div class="mb-3">
                        <label for="nome" class="form-label">Nome</label>
                        <div class="input-group">
                            <input type="text" class="form-control" id="nome" v-model="nome"
                                aria-describedby="basic-addon3 basic-addon4" required minlength="3" maxlength="50">
                        </div>
                        <small v-if="nomeError" class="text-danger">{{ nomeError }}</small>
                    </div>
                    <div class="mb-3">
                        <label for="cargaHoraria" class="form-label">Carga Horária Complementar Exigida</label>
                        <div class="input-group">
                            <input type="number" class="form-control" id="cargaHoraria" v-model.number="cargaHoraria"
                                required min="100" max="800">
                        </div>
                        <small v-if="cargaHorariaError" class="text-danger">{{ cargaHorariaError }}</small>
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

const sigla = ref('');
const nome = ref('');
const cargaHoraria = ref(null);

const siglaError = ref('');
const nomeError = ref('');
const cargaHorariaError = ref('');

const formatarSigla = () => {
    sigla.value = sigla.value.toUpperCase().replace(/ /g, '_');
};

watch(sigla, (newValue) => {
    if (newValue.length < 3 || newValue.length > 20) {
        siglaError.value = 'A sigla deve ter entre 3 e 20 caracteres.';
        setTimeout(() => {
            siglaError.value = '';
        }, 3000);
    } else {
        siglaError.value = '';
    }
});

watch(nome, (newValue) => {
    if (newValue.length < 3 || newValue.length > 50) {
        nomeError.value = 'O nome deve ter entre 3 e 50 caracteres.';
        setTimeout(() => {
            siglaError.value = '';
        }, 3000);
    } else {
        nomeError.value = '';
    }
});

watch(cargaHoraria, (newValue) => {
    if (newValue <= 0) {
        cargaHorariaError.value = 'A carga horária não pode ser menor, igual a zero ou conter letras.';
        setTimeout(() => {
            siglaError.value = '';
        }, 3000);
    } else if (newValue < 100 || newValue > 800) {
        cargaHorariaError.value = 'A carga horária deve ser entre 100 e 800 horas.';
        setTimeout(() => {
            siglaError.value = '';
        }, 3000);
    } else {
        cargaHorariaError.value = '';
    }
});


</script>

<!-- ---------------------------------------------------------------------- -->
<!-- CSS                                                                    -->
<!-- ---------------------------------------------------------------------- -->
<style scoped>
* {
    color: #464545;
}
</style>