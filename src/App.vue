<!-- ---------------------------------------------------------------------- -->
<!-- HTML                                                                   -->
<!-- ---------------------------------------------------------------------- -->
<template>
  <div class="wrapper">
    <aside id="sidebar" ref="sidebar" v-if="isActive">
      <div class="d-flex">
        <button class="toggle-btn" type="button" @click="toggleSidebar">
          <i class="lni lni-grid-alt"></i>
        </button>
        <div class="sidebar-logo">
          <a href="#">Godziny</a>
        </div>
      </div>
      <ul class="sidebar-nav">
        <li class="sidebar-item">
          <RouterLink to="/perfil" class="sidebar-link" active-class="active-link">
            <i class="lni lni-user"></i>
            <span>Perfil</span>
          </RouterLink>
        </li>
        <li class="sidebar-item">
          <RouterLink to="/dashboard" class="sidebar-link" active-class="active-link">
            <i class="lni lni-stats-up"></i>
            <span>Dashboard</span>
          </RouterLink>
        </li>
        <li class="sidebar-item">
          <RouterLink to="/atividade" class="sidebar-link" active-class="active-link">
            <i class="lni lni-remove-file"></i>
            <span>Atividade</span>
          </RouterLink>
        </li>
        <li  :class="['sidebar-item', { 'active-parent': isActiveParent }]" v-if="auth.loginGeral.data.usuario.tipo !== 'NORMAL'">
          <a href="#" class="sidebar-link collapsed has-dropdown" data-bs-toggle="collapse" data-bs-target="#auth"
            aria-expanded="false" aria-controls="auth">
            <i class="lni lni-protection"></i>
            <span>Cadastros</span>
          </a>
          <ul id="auth" class="sidebar-dropdown list-unstyled collapse" data-bs-parent="#sidebar">
            <li class="sidebar-item">
                <RouterLink to="/categorias" class="sidebar-link" active-class="active-link">Categorias</RouterLink>
            </li>
            <li class="sidebar-item" >
              <RouterLink to="/usuarios" class="sidebar-link" active-class="active-link">Usuários</RouterLink>
            </li>
            <li class="sidebar-item" >
              <RouterLink to="/cursos" class="sidebar-link" active-class="active-link">Cursos</RouterLink>
            </li>
          </ul>
        </li>
        <li class="sidebar-item">
          <a href="#" class="sidebar-link collapsed has-dropdown" data-bs-toggle="collapse" data-bs-target="#multi"
            aria-expanded="false" aria-controls="multi">
            <i class="lni lni-layout"></i>
            <span>Multi Level</span>
          </a>
          <ul id="multi" class="sidebar-dropdown list-unstyled collapse" data-bs-parent="#sidebar">
            <li class="sidebar-item">
              <a href="#" class="sidebar-link collapsed" data-bs-toggle="collapse" data-bs-target="#multi-two"
                aria-expanded="false" aria-controls="multi-two">
                Two Links
              </a>
              <ul id="multi-two" class="sidebar-dropdown list-unstyled collapse">
                <li class="sidebar-item">
                  <a href="#" class="sidebar-link">Link 1</a>
                </li>
                <li class="sidebar-item">
                  <a href="#" class="sidebar-link">Link 2</a>
                </li>
              </ul>
            </li>
          </ul>
        </li>
        <li class="sidebar-item">
          <a href="#" class="sidebar-link">
            <i class="lni lni-cog"></i>
            <span>Setting</span>
          </a>
        </li>
      </ul>
      <div class="sidebar-footer">
        <RouterLink to="/login" class="sidebar-link" active-class="active-link" @click="trocaIsActive">
          <i class="lni lni-exit"></i>
          <span>Logout</span>
        </RouterLink>
      </div>
    </aside>

    <div class="main p-3">
      <RouterView @update-is-active="handleIsActiveUpdate" />
    </div>
  </div>
</template>

<!-- ---------------------------------------------------------------------- -->
<!-- JavaScript                                                             -->
<!-- ---------------------------------------------------------------------- -->
<script setup>
import { ref } from 'vue';
import { RouterLink, RouterView,useRouter } from 'vue-router';
import { useRoute } from 'vue-router';
import { computed } from 'vue';
import auth from './lib/autentication';

const route = useRoute();

const isActiveParent = computed(() => {
  const activePaths = ['/categorias', '/usuarios', '/cursos'];
  return activePaths.includes(route.path);
});

const isActive = ref(false);
const sidebar = ref(null);
const router = useRouter();
// Função para alternar a barra lateral
function toggleSidebar() {
  if (sidebar.value) {
    sidebar.value.classList.toggle('expand');
  }
}

// Função para atualizar o estado com base no evento emitido pelo filho
function handleIsActiveUpdate(newValue) {
  isActive.value = newValue;
}

//função para invertelar a logica isActive

function trocaIsActive(){
  isActive.value = !isActive.value
  router.push({ name: 'login' });
}


</script>

  
<!-- ---------------------------------------------------------------------- -->
<!-- CSS                                                                    -->
<!-- ---------------------------------------------------------------------- -->
  <style scoped>
  @import url('https://fonts.googleapis.com/css2?family=Poppins:wght@400;600&display=swap');
  
  ::after,
  ::before {
    box-sizing: border-box;
    margin: 0;
    padding: 0;
  }
  
  a {
    text-decoration: none;
  }
  
  li {
    list-style: none;
  }
  
  h1 {
    font-weight: 600;
    font-size: 1.5rem;
  }
  
  body {
    font-family: 'Poppins', sans-serif;
  }
  
  .wrapper {
    display: flex;
  }
  
  .main {
    min-height: 100vh;
    width: 100%;
    overflow: hidden;
    transition: all 0.35s ease-in-out;
  }
  
  #sidebar {
    width: 70px;
    min-width: 70px;
    z-index: 1000;
    transition: all .25s ease-in-out;
    background-color: #235cd7;
    display: flex;
    flex-direction: column;
  }
  
  #sidebar.expand {
    width: 260px;
    min-width: 260px;
  }
  
  .toggle-btn {
    background-color: transparent;
    cursor: pointer;
    border: 0;
    padding: 1rem 1.5rem;
  }
  
  .toggle-btn i {
    font-size: 1.5rem;
    color: #FFF;
  }
  
  .sidebar-logo {
    margin: auto 0;
  }
  
  .sidebar-logo a {
    color: #FFF;
    font-size: 1.15rem;
    font-weight: 600;
  }
  
  #sidebar:not(.expand) .sidebar-logo,
  #sidebar:not(.expand) a.sidebar-link span {
    display: none;
  }
  
  .sidebar-nav {
    padding: 2rem 0;
    flex: 1 1 auto;
  }
  
  a.sidebar-link {
    padding: .625rem 1.625rem;
    color: #FFF;
    display: block;
    font-size: 0.9rem;
    white-space: nowrap;
    border-left: 3px solid transparent;
  }
  
  .sidebar-link i {
    font-size: 1.1rem;
    margin-right: .75rem;
  }
  
  a.sidebar-link:hover {
    background-color: rgba(255, 255, 255, .075);
    border-left: 3px solid #3b7ddd;
  }
  
  .sidebar-item {
    position: relative;
  }

  .active-link {
  background-color: rgba(255, 255, 255, 0.15);
  border-left: 3px solid #3b7ddd;
  }

  .active-parent {
  background-color: rgba(255, 255, 255, 0.15);
  border-left: 3px solid #3b7ddd;
  }

  
  #sidebar:not(.expand) .sidebar-item .sidebar-dropdown {
    position: absolute;
    top: 0;
    left: 70px;
    background-color: #0e2238;
    padding: 0;
    min-width: 15rem;
    display: none;
  }
  
  #sidebar:not(.expand) .sidebar-item:hover .has-dropdown+.sidebar-dropdown {
    display: block;
    max-height: 15em;
    width: 100%;
    opacity: 1;
  }
  
  #sidebar.expand .sidebar-link[data-bs-toggle="collapse"]::after {
    border: solid;
    border-width: 0 .075rem .075rem 0;
    content: "";
    display: inline-block;
    padding: 2px;
    position: absolute;
    right: 1.5rem;
    top: 1.4rem;
    transform: rotate(-135deg);
    transition: all .2s ease-out;
  }
  
  #sidebar.expand .sidebar-link[data-bs-toggle="collapse"].collapsed::after {
    transform: rotate(45deg);
    transition: all .2s ease-out;
  }
  </style>
  