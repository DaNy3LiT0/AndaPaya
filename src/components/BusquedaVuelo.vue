<template>
  <div class="container">
    <!-- Primera línea: tipo de viaje y clase -->
    <div class="row mb-3 d-flex align-items-center">
      <div class="col-md-3">
        <div class="form-check form-check-inline">
          <input class="form-check-input" type="radio" id="roundTrip" name="tipoViaje" v-model="isRoundTrip"
            :value="true" />
          <label class="form-check-label" for="roundTrip">
            Ida y Vuelta
          </label>
        </div>
        <div class="form-check form-check-inline">
          <input class="form-check-input" type="radio" id="oneWay" name="tipoViaje" v-model="isRoundTrip"
            :value="false" />
          <label class="form-check-label" for="oneWay">
            Solo Ida
          </label>
        </div>
      </div>
      <div class="col-md-2">
        <div class="dropdown">
          <a class="nav-link dropdown-toggle" href="#" id="classDropdown" role="button" data-bs-toggle="dropdown"
            aria-expanded="false">
            {{ classType ? classType.charAt(0).toUpperCase() + classType.slice(1) : 'Elija una Clase' }}
          </a>
          <ul class="dropdown-menu" aria-labelledby="classDropdown">
            <li>
              <a class="dropdown-item" href="#" @click="selectClass('economica')">Económica</a>
            </li>
            <li>
              <a class="dropdown-item" href="#" @click="selectClass('ejecutiva')">Ejecutiva</a>
            </li>
            <li>
              <a class="dropdown-item" href="#" @click="selectClass('primera')">Primera Clase</a>
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
            <input type="text" class="form-control py-3" placeholder="¿De dónde?" v-model="origin" />
          </div>
          <div class="col-6">
            <input type="text" class="form-control py-3" placeholder="¿A dónde?" v-model="destination" />
          </div>
        </div>
      </div>

      <!-- Columna de fechas, pasajeros y botón -->
      <div class="col-md-6">
        <div class="row align-items-center">
          <div class="col-md-3">
            <input type="date" class="form-control py-3" v-model="departureDate" placeholder="Fecha de salida" />
          </div>

          <div class="col-md-3" v-if="isRoundTrip">
            <input type="date" class="form-control py-3" v-model="returnDate" placeholder="Fecha de regreso" />
          </div>

          <div class="col-md-3">
            <input type="number" class="form-control py-3" v-model="travellers" min="1" max="10" placeholder="Pasajeros" />
          </div>

          <div class="col-md-3">
            <button type="button" class="btn btn-warning" @click="BuscaVuelos">
              <strong>Buscar Vuelos</strong>
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      origin: '',
      destination: '',
      departureDate: '',
      returnDate: '',
      travellers: '',
      classType: '',
      isRoundTrip: true,
    };
  },
  methods: {
    BuscaVuelos() {
      console.log('Buscando vuelos...', {
        origen: this.origin,
        destino: this.destination,
        fechaSalida: this.departureDate,
        fechaRegreso: this.isRoundTrip ? this.returnDate : null,
        pasajeros: this.travellers,
        clase: this.classType,
      });
    },
    selectClass(classType) {
      this.classType = classType; // Actualiza la clase seleccionada
    },
  },
  watch: {
    isRoundTrip(newVal) {
      if (!newVal) this.returnDate = ''; // Limpiar la fecha de regreso si no es ida y vuelta
    },
  },
};
</script>

<style scoped></style>
