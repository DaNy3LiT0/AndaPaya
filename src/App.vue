<template>
  <div id="App" class="d-flex flex-column min-vh-100">
    <header>
      <HeaderComponent @login="openLoginModal" />
    </header>

    <main class="flex-fill d-flex flex-column align-items-center p-4">
      <div class="border rounded p-4 col-10 bg-white">
        <BusquedaVuelo />
      </div>
      <div class="border rounded p-4 col-10 bg-white my-4">
        <FormPasajero />
      </div>
      <div class="border rounded p-4 col-10 bg-white">
        <FormPago />
      </div>
    </main>

    <footer class="d-flex bg-light text-danger py-3 justify-content-center">
      <p>&copy; 2023 Mi Aplicación de Viajes. Todos los derechos reservados.</p>
    </footer>

    <!-- Modal Login -->
    <div v-show="showLoginModal" class="modal fade show" tabindex="-1" aria-labelledby="loginModalLabel"
      aria-hidden="true" style="display: block; background-color: rgba(0, 0, 0, 0.5);">
      <div class="modal-dialog">
        <div class="modal-content">
          <div class="modal-header">
            <h5 class="modal-title" id="loginModalLabel">Iniciar sesión</h5>
            <button type="button" class="btn-close" @click="closeLoginModal" aria-label="Close">
            </button>
          </div>
          <div class="modal-body">
            <form @submit.prevent="handleLoginSubmit">
              <div class="mb-3">
                <label for="email" class="form-label">Correo electrónico</label>
                <input type="email" class="form-control" id="email" v-model="loginEmail" required />
              </div>
              <div class="mb-3">
                <label for="password" class="form-label">Contraseña</label>
                <input type="password" class="form-control" id="password" v-model="loginPassword" required />
              </div>
              <button type="submit" class="btn btn-primary">Iniciar sesión</button>
            </form>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import HeaderComponent from './components/HeaderComponent.vue';
import BusquedaVuelo from './components/BusquedaVuelo.vue';
import FormPasajero from './components/FormPasajero.vue';
import FormPago from './components/FormPago.vue';

export default {
  components: {
    HeaderComponent,
    BusquedaVuelo,
    FormPasajero,
    FormPago,
  },
  data() {
    return {
      loginEmail: '',
      loginPassword: '',
      showLoginModal: false, // Variable que controla la visibilidad del modal
    };
  },
  methods: {
    // Método para abrir el modal de login
    openLoginModal() {
      console.log("Abriendo modal de login...");
      this.showLoginModal = true; // Establece el estado de visibilidad del modal
    },
    // Método para manejar el envío del formulario de login
    handleLoginSubmit() {
      console.log('Iniciando sesión con:', this.loginEmail, this.loginPassword);
      // Aquí puedes agregar la lógica de autenticación
      this.closeLoginModal();
    },
    // Método para cerrar el modal
    closeLoginModal() {
      this.showLoginModal = false; // Cambia el estado para ocultar el modal
    },
  },
};
</script>

<style scoped>
#App {
  background-color: rgb(0, 0, 50);
}
</style>
