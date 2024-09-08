<template>
  <div class="container py-4">
    <div class="row">
      <div class="col">
        <h2 class="text-secondary">Atividades</h2>
      </div>

      <div class="col m-2">
        <div class="text-end">
          <!-- Botão para abrir o modal -->
          <button type="button" v-if="auth.loginGeral.data.usuario.tipo !== 'ADM'" class="btn btn-primary"
            data-bs-toggle="modal" data-bs-target="#atividadePopup" @click="showPopup">
            Adicionar atividade
          </button>
          <AdicionarAtividade @atividade-adicionada="atualizarAtividades" />
        </div>
      </div>
    </div>

    <div class="h-100 p-5 bg-light border rounded-3">
      <form class="row justify-content-end">
        <div class="col-auto">
          <label class="text-secondary">Pesquisar tarefa</label>
          <input type="text" class="form-control" v-model="searchTitle" placeholder="Pesquisar">
        </div>
        <div class="col-auto" v-if="auth.loginGeral.data.usuario.tipo !== 'NORMAL'">
          <label class="text-secondary">Ativo</label>
          <select class="form-select form-select" aria-label="Small select example" v-model="selecao">
            <option value="1">atividades</option>
            <option value="2">categorias</option>
            <option value="3">cursos</option>
            <option value="4">usuarios</option>
          </select>
        </div>
      </form>

      <!-- Listagem das atividades -->
      <div class="row mt-3">
        <!-- Selecione a coluna apropriada com base no valor de 'selecao' -->
        <div class="col" v-if="selecao == '1'">
          <div class="row">
            <div class="col-3 p-3 mt" v-for="(atividade, index) in componentProps.atividades" :key="index">
              <AtividadeComuns data-bs-toggle="modal" data-bs-target="#exampleModal" v-bind="{ atividade, selecao }" />
            </div>
            <!-- Mensagem quando não há atividades cadastradas -->
            <div v-if="componentProps.atividades.length === 0" class="text-center">
              <h5>Não há atividades cadastradas!</h5>
            </div>
          </div>
        </div>
        <div class="col" v-else-if="selecao == '2' && auth.loginGeral.data.usuario.tipo !== 'NORMAL'">
          <div class="row">
            <div class="col-3 p-3 mt" v-for="(categoria, index) in componentProps.categorias" :key="index">
              <AtividadeComuns data-bs-toggle="modal" data-bs-target="#exampleModal" v-bind="{ categoria, selecao }" />
            </div>
          </div>
        </div>
        <div class="col" v-else-if="selecao == '3' && auth.loginGeral.data.usuario.tipo !== 'NORMAL'">
          <div class="row">
            <div class="col-3 p-3 mt" v-for="(curso, index) in componentProps.cursos" :key="index">
              <AtividadeComuns data-bs-toggle="modal" data-bs-target="#exampleModal" v-bind="{ curso, selecao }" />
            </div>
          </div>
        </div>
        <div class="col" v-else-if="selecao == '4' && auth.loginGeral.data.usuario.tipo !== 'NORMAL'">
          <div class="row">
            <div class="col-3 p-3 mt" v-for="(usuario, index) in componentProps.usuarios" :key="index">
              <AtividadeComuns data-bs-toggle="modal" data-bs-target="#exampleModal" v-bind="{ usuario, selecao }" />
            </div>
          </div>
        </div>
      </div>

      <!-- Modal -->
      <div class="modal fade" id="exampleModal" tabindex="-1" aria-labelledby="exampleModalLabel" aria-hidden="true">
        <div class="modal-dialog">
          <div class="modal-content">
            <div class="modal-header">
              <h1 class="modal-title fs-5" id="exampleModalLabel">Modal title</h1>
              <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
            </div>
            <div class="modal-body">
              ...
            </div>
            <div class="modal-footer">
              <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Close</button>
              <button type="button" class="btn btn-primary">Save changes</button>
            </div>
          </div>
        </div>
      </div>

      <div class="row mt-5 justify-content-end">
        <div class="col-auto">
          <PaginacaoComuns @valorAtualizado="atualizarPagina" :totalPaginas="totalPaginas" :paginaAtual="page.value" :key="selecao"/>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import PaginacaoComuns from '@/components/PaginacaoComuns.vue';
import AdicionarAtividade from '@/components/AdicionarAtividade.vue';
import AtividadeComuns from '@/components/AtividadeComuns.vue';
import { ref, onMounted, watch } from 'vue';
import { Modal } from 'bootstrap';
import axios from 'axios';
import auth from '../lib/autentication';

const myModal = ref(null);
const atividades = ref([]);
const selecao = ref('1');
const categorias = ref([]);
const cursos = ref([]);
const usuarios = ref([]);
const searchTitle = ref('');
const page = ref(0);
const size = ref(4);
const totalPaginas = ref(null)


// Objeto para armazenar as propriedades que serão passadas para o componente AtividadeComuns
const componentProps = ref({
  atividades: [],
  categorias: [],
  cursos: [],
  usuarios: []
});

// Função para atualizar componentProps
const updateComponentProps = () => {
  componentProps.value = {
    atividades: atividades.value,
    categorias: categorias.value,
    cursos: cursos.value,
    usuarios: usuarios.value,
    selecao: selecao.value
  };
};

// função para determinar o total de paginação
const numPaginacao = (totalElements) => {
  totalPaginas.value = Math.floor(totalElements / size.value);
  if ((totalElements % size.value) != 0) {
    totalPaginas.value = totalPaginas.value + 1
  }
  
}
const buscarAtividades = async (titulo) => {
  console.log(selecao.value)
  switch (selecao.value) {

    case "1":
      try {
        const pageableParams = {
          page: page.value, // número da página
          size: size.value, // itens por página
          sort: 'titulo,asc', // ordenação
        };

        const params = new URLSearchParams(pageableParams).toString();
        const updateParams = {
          usuarioNome: null,
          titulo: titulo,
          status: null,
          categoria: null

        }

        const response = await axios.post(
          `http://localhost:8080/atividade/pesquisar?${params}`,
          updateParams,
          {
            headers: {
              Authorization: `Bearer ${auth.loginGeral.data.token}`,
            },
          }
        );

        atividades.value = response.data.content;
        numPaginacao(response.data.totalElements);
      } catch (error) {
        console.error('Erro ao buscar atividades:', error);
      }
      break;
    case "2":
      try {
        const pageableParams = {
          page: page.value, // número da página
          size: size.value, // itens por página
          sort: 'nome,asc', // ordenação
        };

        const params = new URLSearchParams(pageableParams).toString();
        const updateParams = {
          cursoSigla: null,
          nome: titulo

        }

        const response = await axios.post(
          `http://localhost:8080/categoria/pesquisar?${params}`,
          updateParams,
          {
            headers: {
              Authorization: `Bearer ${auth.loginGeral.data.token}`,
            },
          }
        );

        categorias.value = response.data.content;
        numPaginacao(response.data.totalElements);
      } catch (error) {
        console.error('Erro ao buscar atividades:', error);
      }
      break;
    case "3":
      try {
        const pageableParams = {
          page: page.value, // número da página
          size: size.value, // itens por página
          sort: 'nome,asc', // ordenação
        };

        const params = new URLSearchParams(pageableParams).toString();
        const updateParams = {
          sigla: null,
          nome: titulo

        }

        const response = await axios.post(
          `http://localhost:8080/curso/pesquisar?${params}`,
          updateParams,
          {
            headers: {
              Authorization: `Bearer ${auth.loginGeral.data.token}`,
            },
          }
        );

        cursos.value = response.data.content;
        numPaginacao(response.data.totalElements);
      } catch (error) {
        console.error('Erro ao buscar cursos:', error);
      }
      break;
  }

};

// Observa mudanças na variável searchTitle
watch(searchTitle, (novoValor) => {
  buscarAtividades(novoValor);
});

// Carregamento inicial dos dados quando o componente é montado
onMounted(async () => {
  // Inicializa o modal do Bootstrap
  myModal.value = new Modal(document.getElementById('atividadePopup'));



  try {
    // Carregar atividades do backend
    const pageableParams = {
      page: page.value, // número da página
      size: size.value, // itens por página
      sort: 'titulo,asc', // ordenação
    };
    const params = new URLSearchParams(pageableParams).toString();

    // carregamento dos valores das atividades ----------------------------------------------------------------------------------------------------------------------------------
    const response = await axios.post(
      `http://localhost:8080/atividade/pesquisar?${params}`,
      {},
      {
        headers: {
          Authorization: `Bearer ${auth.loginGeral.data.token}`,
        },
      }
    );

    atividades.value = response.data.content;
    numPaginacao(response.data.totalElements);
  } catch (error) {
    console.error('Erro ao buscar atividades:', error);
  }

  if (auth.loginGeral.data.usuario.tipo !== 'NORMAL') {
    // carregamento dos valores das categorias ----------------------------------------------------------------------------------------------------------------------------------
    try {
      // Carregar categorias do backend
      const pageableParams = {
        page: page.value, // número da página
        size: size.value, // itens por página
        sort: 'nome,asc', // ordenação
      };
      const params = new URLSearchParams(pageableParams).toString();
      const response = await axios.post(
        `http://localhost:8080/categoria/pesquisar?${params}`,
        {},
        {
          headers: {
            Authorization: `Bearer ${auth.loginGeral.data.token}`,
          },
        }
      );

      categorias.value = response.data.content;
    } catch (error) {
      console.error('Erro ao buscar categorias:', error);
    }

    // carregamento dos valores dos cursos ----------------------------------------------------------------------------------------------------------------------------------
    try {
        // Carregar atividades do backend
        const pageableParams = {
        page: page.value, // número da página
        size: size.value, // itens por página
        sort: 'nome,asc', // ordenação
      };
      const params = new URLSearchParams(pageableParams).toString();
        const response = await axios.post(
          `http://localhost:8080/curso/pesquisar?${params}`,
          {},
          {
            headers: {
              Authorization: `Bearer ${auth.loginGeral.data.token}`,
            },
          }
        );

      cursos.value = response.data.content;
    } catch (error) {
      console.error('Erro ao buscar cursos:', error);
    }
    // carregamento dos valores dos usuarios ----------------------------------------------------------------------------------------------------------------------------------
    try {
      const response = await axios.post(
        'http://localhost:8080/usuario/pesquisar',
        {},
        {
          headers: {
            Authorization: `Bearer ${auth.loginGeral.data.token}`,
          },
        }
      );


      usuarios.value = response.data.content;
    } catch (error) {
      console.error('Erro ao buscar usuarios:', error);
    }
  }


  // Atualiza componentProps com os dados carregados
  updateComponentProps();
});

// Atualiza componentProps quando as propriedades mudam
watch([atividades, categorias, cursos, usuarios], () => {
  updateComponentProps();
}, { deep: true });

// Exibe o modal quando o botão "Adicionar atividade" é clicado
const showPopup = () => {
  if (myModal.value) {
    myModal.value.show();
  }
};

// Função para adicionar uma nova atividade e atualizar a lista
const atualizarAtividades = async (formData) => {
  try {
    const response = await axios.post(
      'http://localhost:8080/atividade',
      formData,
      {
        headers: {
          Authorization: `Bearer ${auth.loginGeral.data.token}`,
          "Content-Type": "multipart/form-data",
        },
      }
    );

    console.log('Atividade adicionada com sucesso', response);
    // Recarregar as atividades após adicionar uma nova
    buscarAtividades(null);
  } catch (error) {
    console.error('Erro ao adicionar atividade', error);
  }
};

const atualizarPagina = (data) => {
  page.value = data
  buscarAtividades(null);
}
</script>

<style scoped>
* {
  color: #464545;
}
</style>
