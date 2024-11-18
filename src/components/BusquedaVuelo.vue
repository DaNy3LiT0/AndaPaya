<template>
  <div class="container">
    <!-- Primera línea: tipo de viaje y clase -->
    <div class="row mb-3 d-flex align-items-center">
      <div class="col-md-3">
        <div class="form-check form-check-inline">
          <input class="form-check-input" type="radio" id="idaVuelta" name="tipoViaje" v-model="esIdaVuelta"
            :value="true" />
          <label class="form-check-label" for="idaVuelta">
            Ida y Vuelta
          </label>
        </div>
        <div class="form-check form-check-inline">
          <input class="form-check-input" type="radio" id="soloIda" name="tipoViaje" v-model="esIdaVuelta"
            :value="false" />
          <label class="form-check-label" for="soloIda">
            Solo Ida
          </label>
        </div>
      </div>
      <div class="col-md-2">
        <div class="dropdown">
          <a class="nav-link dropdown-toggle" href="#" id="dropdownClase" role="button" data-bs-toggle="dropdown"
            aria-expanded="false">
            {{ tipoClase ? tipoClase.charAt(0).toUpperCase() + tipoClase.slice(1) : 'Elija una Clase' }}
          </a>
          <ul class="dropdown-menu" aria-labelledby="dropdownClase">
            <li>
              <a class="dropdown-item" href="#" @click="seleccionarClase('economica')">Económica</a>
            </li>
            <li>
              <a class="dropdown-item" href="#" @click="seleccionarClase('ejecutiva')">Ejecutiva</a>
            </li>
            <li>
              <a class="dropdown-item" href="#" @click="seleccionarClase('primera')">Primera Clase</a>
            </li>
          </ul>
        </div>
      </div>
    </div>

    <!-- Segunda línea: destinos y fechas -->
    <div class="row">
      <!-- Columna de destinos -->
      <div class="col-md-6">
        <div class="row">
          <div class="col-6">
            <label for="origen" class="form-label small mb-0">Ciudad de Origen</label>
            <div class="position-relative">
              <input type="text" class="form-control py-3" id="origen" v-model="origen" placeholder="¿De dónde?"
                @input="filtrarCiudades('origen')" />
              <ul v-if="ciudadesFiltradas.origen.length > 0" class="list-group position-absolute"
                style="z-index: 1000; width: 100%;">
                <li v-for="ciudad in ciudadesFiltradas.origen" :key="ciudad.id"
                  @click="seleccionarCiudad('origen', ciudad)" class="list-group-item list-group-item-action">
                  {{ ciudad.ciudad }}, {{ ciudad.nompais }}
                </li>
              </ul>
            </div>
          </div>
          <div class="col-6">
            <label for="destino" class="form-label small mb-0">Ciudad de Destino</label>
            <div class="position-relative">
              <input type="text" class="form-control py-3" id="destino" v-model="destino" placeholder="¿A dónde?"
                @input="filtrarCiudades('destino')" />
              <ul v-if="ciudadesFiltradas.destino.length > 0" class="list-group position-absolute"
                style="z-index: 1000; width: 100%;">
                <li v-for="ciudad in ciudadesFiltradas.destino" :key="ciudad.id"
                  @click="seleccionarCiudad('destino', ciudad)" class="list-group-item list-group-item-action">
                  {{ ciudad.ciudad }}, {{ ciudad.nompais }}
                </li>
              </ul>
            </div>
          </div>
        </div>
        <!-- Mensaje de error -->
        <div v-if="mensajeError" class="text-danger mt-2">
          {{ mensajeError }}
        </div>
      </div>

      <!-- Columna de fechas, pasajeros y botón -->
      <div class="col-md-6">
        <div class="row align-items-end">
          <div class="col-md-4">
            <label for="fechaSalida" class="form-label small mb-0">Fecha Salida</label>
            <input type="date" class="form-control py-3" id="fechaSalida" v-model="fechaSalida" />
          </div>

          <div class="col-md-4" v-if="esIdaVuelta">
            <label for="fechaRegreso" class="form-label small mb-0">Fecha Regreso</label>
            <input type="date" class="form-control py-3" id="fechaRegreso" v-model="fechaRegreso" :min="fechaSalida" />
          </div>

          <div class="col-md-2">
            <label for="pasajeros" class="form-label small mb-0">Pasajeros</label>
            <input type="number" class="form-control py-3" id="pasajeros" v-model="pasajeros" min="1" max="10"
              placeholder="1" />
          </div>

          <div class="col-md-2 d-flex align-items-end">
            <button type="button" class="btn btn-warning" @click="buscarVuelos">
              <strong>Buscar Vuelos</strong>
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import aeropuertos from '@/data/aeropuertos.json';

export default {
  data() {
    return {
      origen: '',
      destino: '',
      fechaSalida: '',
      fechaRegreso: '',
      pasajeros: '',
      tipoClase: '',
      esIdaVuelta: true,
      ciudadesFiltradas: {
        origen: [],
        destino: [],
      },
      aeropuertos: aeropuertos,
      mensajeError: '', // Nueva propiedad para el mensaje de error
    };
  },
  methods: {
    filtrarCiudades(tipo) {
      const input = tipo === 'origen' ? this.origen : this.destino;
      const ciudades = this.aeropuertos.filter(aeropuerto =>
        aeropuerto.ciudad.toLowerCase().includes(input.toLowerCase())
      );
      this.ciudadesFiltradas[tipo] = ciudades;
    },
    seleccionarCiudad(tipo, ciudad) {
      if (tipo === 'origen') {
        this.origen = `${ciudad.ciudad}, ${ciudad.nompais || 'País desconocido'}`; // Almacena la ciudad y el país
      } else {
        if (`${ciudad.ciudad}, ${ciudad.nompais || 'País desconocido'}` === this.origen) {
          this.mensajeError = 'La ciudad de destino no puede ser la misma que la ciudad de origen.';
          this.destino = ''; // Limpiar el destino si es igual al origen
        } else {
          this.destino = `${ciudad.ciudad}, ${ciudad.nompais || 'País desconocido'}`; // Almacena la ciudad y el país
          this.mensajeError = ''; // Limpiar el mensaje de error si son diferentes
        }
      }
      this.ciudadesFiltradas[tipo] = []; // Limpiar las sugerencias
    },
    buscarVuelos() {
      // Validar que todos los campos estén completados
      if (!this.origen || !this.destino || !this.fechaSalida || (this.esIdaVuelta && !this.fechaRegreso) || !this.pasajeros) {
        this.mensajeError = 'Por favor, completa todos los campos requeridos.';
        return;
      }

      // Si todos los campos están completos, mostrar el modal
      this.mostrarModal = true; // Variable para controlar el modal
    },
    confirmarBusqueda() {
      console.log('Buscando vuelos...', {
        origen: this.origen,
        destino: this.destino,
        fechaSalida: this.fechaSalida,
        fechaRegreso: this.esIdaVuelta ? this.fechaRegreso : null,
        pasajeros: this.pasajeros,
        clase: this.tipoClase,
      });
      this.mostrarModal = false; // Cerrar el modal después de confirmar
    },
    cancelarBusqueda() {
      this.mostrarModal = false; // Cerrar el modal si se cancela
    },
    seleccionarClase(tipoClase) {
      this.tipoClase = tipoClase; // Actualiza la clase seleccionada
    },
  },
  watch: {
    esIdaVuelta(nuevoValor) {
      if (!nuevoValor) this.fechaRegreso = '';
    }
  }
};
</script>

<style scoped></style>