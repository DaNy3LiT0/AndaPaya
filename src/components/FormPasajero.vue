<template>
  <div class="container">
    <h3>Datos del Pasajero</h3>
    <form @submit.prevent="enviarDatosPasajero">
      <div class="mb-3">
        <label for="nombreCompleto" class="form-label small mb-0">Nombre Completo</label>
        <input type="text" id="nombreCompleto" v-model="pasajero.nombreCompleto" @input="validarNombreCompleto"
          class="form-control" :class="{ 'is-invalid': errores.nombreCompleto }" required />
        <div v-if="errores.nombreCompleto" class="text-danger small" v-cloak>
          {{ errores.nombreCompleto }}
        </div>
      </div>

      <div class="row mb-3">
        <div class="col">
          <label for="nacionalidad" class="form-label small mb-0">Nacionalidad</label>
          <select id="nacionalidad" v-model="pasajero.nacionalidad" class="form-select" required>
            <option value="" disabled>Seleccione un país</option>
            <option v-for="pais in paises" :key="pais.codigo" :value="pais.codigo">
              {{ pais.pais }}
            </option>
          </select>
        </div>

        <div class="col">
          <label for="numeroPasaporte" class="form-label small mb-0">Número de Pasaporte</label>
          <input type="text" id="numeroPasaporte" v-model="pasajero.numeroPasaporte" @change="validarNumeroPasaporte"
            class="form-control" :class="{ 'is-invalid': errores.numeroPasaporte }"
            :placeholder="`Ejemplo: ${obtenerEjemploPasaporte(pasajero.nacionalidad)}`" required />
          <div v-if="errores.numeroPasaporte" class="text-danger small" v-cloak>
            {{ errores.numeroPasaporte }}
          </div>
        </div>

        <div class="col">
          <label for="fechaNacimiento" class="form-label small mb-0">Fecha de Nacimiento</label>
          <input type="date" id="fechaNacimiento" v-model="pasajero.fechaNacimiento" @change="validarFechaNacimiento"
            class="form-control" required />
          <div v-if="errores.fechaNacimiento" class="text-danger">
            {{ errores.fechaNacimiento }}
          </div>
        </div>
      </div>

      <div class="d-flex justify-content-end">
        <button type="submit" class="btn btn-primary"
          >
          Cargar Pasajero
        </button>
      </div>
    </form>
  </div>
</template>

<script>
import paisesData from "@/data/pasaportes.json"; // Asegúrate de que la ruta sea correcta

export default {
  data() {
    return {
      pasajero: {
        nombreCompleto: "",
        numeroPasaporte: "",
        fechaNacimiento: "",
        nacionalidad: "",
      },
      errores: {
        nombreCompleto: "",
        numeroPasaporte: "",
        fechaNacimiento: "",
      },
      paises: paisesData, // Asigna los datos del JSON aquí
    };
  },
  methods: {
    validarNombreCompleto() {
      const regex = /^[A-Za-z\s]{3,}$/;
      this.errores.nombreCompleto = regex.test(this.pasajero.nombreCompleto)
        ? ""
        : "El nombre completo debe tener al menos 3 letras y solo contener letras y espacios.";
    },
    validarNumeroPasaporte() {
      const paisSeleccionado = this.paises.find(pais => pais.codigo === this.pasajero.nacionalidad);
      let regex;

      if (paisSeleccionado) {
        switch (paisSeleccionado.codigo) {
          case "ARG":
            regex = /^[A-Z]{3}\d{6}$/; // Formato para Argentina
            break;
          case "USA":
            regex = /^\d{9}$/; // Formato para Estados Unidos
            break;
          case "GBR":
          case "DEU":
          case "FRA":
            regex = /^[A-Za-z0-9]{9}$/; // Formato para Reino Unido, Alemania, Francia
            break;
          case "JPN":
            regex = /^[A-Z]{2}\d{7}$/; // Formato para Japón
            break;
          case "IND":
          case "AUS":
          case "MYS":
          case "PAK":
          case "THA":
          case "IDN":
          case "PHL":
            regex = /^[A-Z]\d{8}$/; // Formato para India, Australia, Malasia, Pakistán, Tailandia, Indonesia, Filipinas
            break;
          case "CHN":
          case "MEX":
          case "ZAF":
            regex = /^[A-Z]\d{8}$/; // Formato para China, México, Sudáfrica
            break;
          case "BRA":
          case "CHL":
          case "COL":
          case "PER":
          case "VEN":
          case "CAN":
          case "KOR":
          case "SAU":
            regex = /^[A-Z]{2}\d{6}$/; // Formato para Brasil, Chile, Colombia, Perú, Venezuela, Canadá, Corea del Sur, Arabia Saudita
            break;
          case "RUS":
            regex = /^\d{9} \d{9}$/; // Formato para Rusia
            break;
          case "EGY":
            regex = /^\d{9}$/; // Formato para Egipto
            break;
          case "TUR":
            regex = /^[A-Z]\d{8}$/; // Formato para Turquía
            break;
          case "NZL":
            regex = /^[A-Z]{2}\d{6}$/; // Formato para Nueva Zelanda
            break;
          default:
            regex = /^[A-Za-z0-9]{6,12}$/; // Formato por defecto
        }

        this.errores.numeroPasaporte = regex.test(this.pasajero.numeroPasaporte)
          ? ""
          : `El número de pasaporte no coincide con el formato esperado para ${paisSeleccionado.pais}.`;
      } else {
        this.errores.numeroPasaporte = "Seleccione una nacionalidad válida.";
      }
    },
    validarFechaNacimiento() {
      const fechaNacimiento = new Date(this.pasajero.fechaNacimiento);
      const hoy = new Date();

      // Calcular la fecha máxima de nacimiento permitida
      const fechaMaximaNacimiento = new Date(hoy.getFullYear() - 100, hoy.getMonth(), hoy.getDate());

      this.errores.fechaNacimiento =
        fechaNacimiento > hoy
          ? "La fecha de nacimiento no puede ser futura."
          : fechaNacimiento < fechaMaximaNacimiento
            ? "El pasajero no puede tener más de 100 años."
            : fechaNacimiento > new Date(hoy.getFullYear() - 18, hoy.getMonth(), hoy.getDate())
              ? "El pasajero debe tener al menos 18 años."
              : "";
    },
    obtenerEjemploPasaporte(codigo) {
      const pais = this.paises.find((pais) => pais.codigo === codigo);
      return pais ? pais.ejemplo : "Formato no definido";
    },
    enviarDatosPasajero() {
      if (
        !this.errores.nombreCompleto &&
        !this.errores.numeroPasaporte &&
        !this.errores.fechaNacimiento
      ) {
        console.log("Datos del pasajero válidos:", this.pasajero);
        // Procede al siguiente formulario
      }
    },
  },
};
</script>

<style scoped>
/* Usando estilos de Bootstrap únicamente */
</style>
