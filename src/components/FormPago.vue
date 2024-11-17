<template>
  <div>
    <h3>Datos de Pago</h3>
    <form @submit.prevent="submitPaymentData">
      <div class="row">
        <div class="col-8">
          <div class="mb-3">
            <label for="cardNumber" class="form-label small mb-0">Número de Tarjeta</label>
            <input type="text" id="cardNumber" v-model="payment.cardNumber" @input="validateCardNumber"
              class="form-control" required />
            <div v-if="errors.cardNumber" class="text-danger">{{ errors.cardNumber }}</div>
          </div>
          <div class="mb-3">
            <label for="cardName" class="form-label small mb-0">Nombre en la Tarjeta</label>
            <input type="text" id="cardName" v-model="payment.cardName" @input="validateCardName" class="form-control"
              required />
            <div v-if="errors.cardName" class="text-danger">{{ errors.cardName }}</div>
          </div>
          <div class="row mb-3">
            <div class="col">
              <label for="expiryDate" class="form-label small mb-0">Fecha de Vencimiento (MMAA)</label>
              <input type="text" id="expiryDate" v-model="payment.expiryDate" @input="validateExpiryDate"
                class="form-control" required />
              <div v-if="errors.expiryDate" class="text-danger">{{ errors.expiryDate }}</div>
            </div>

            <div class="col">
              <label for="cvv" class="form-label small mb-0">CVV</label>
              <input type="text" id="cvv" v-model="payment.cvv" @input="validateCVV" class="form-control" required />
              <div v-if="errors.cvv" class="text-danger">{{ errors.cvv }}</div>
            </div>

          </div>
        </div>

        <div class="col-4">
          <div class="">
            <img :src="cardProvider.image" :alt="cardProvider.name" class="mt-3" width="100%" />
          </div>
        </div>
      </div>

      <div class="d-flex justify-content-end">
        <button type="submit" class="btn btn-primary" :disabled="isFormInvalid">Confirmar Pago</button>
      </div>
    </form>
  </div>
</template>

<script>
import tarjetasCredito from '@/data/tarjetasCredito.json'; // Asegúrate de la ruta correcta

export default {
  data() {
    return {
      payment: {
        cardNumber: '',
        expiryDate: '',
        cvv: '',
        cardName: '',
      },
      errors: {
        cardNumber: '',
        expiryDate: '',
        cvv: '',
        cardName: '',
      },
      cardProvider: {
        name: 'Proveedor Desconocido',
        image: require('@/assets/tarjetas/Blanca.png'), // Imagen por defecto
      },
      cardProviders: [], // Para almacenar los proveedores cargados desde el JSON
    };
  },
  created() {
    this.loadCardProviders();
  },
  computed: {
    isFormInvalid() {
      return Object.values(this.errors).some(error => error !== '');
    },
  },
  methods: {
    loadCardProviders() {
      this.cardProviders = tarjetasCredito; // Cargar los proveedores desde el JSON
    },
    validateCardNumber() {
      const regex = /^\d{12,19}$/; // Acepta tarjetas de 12 a 19 dígitos
      this.errors.cardNumber = regex.test(this.payment.cardNumber) ? '' : 'Número de tarjeta inválido.';
      this.detectCardProvider();
    },
    detectCardProvider() {
      const bin = this.payment.cardNumber.slice(0, 6); // Obtener los primeros 6 dígitos

      // Buscar el proveedor en el array de proveedores
      const provider = this.cardProviders.find(p => bin.startsWith(p.BIN));
      if (provider) {
        try {
          // Intentar cargar la imagen basada en la marca
          const image = require(`@/assets/tarjetas/${provider["Marca de carro"]}.png`);
          this.cardProvider = { name: provider["Marca de carro"], image };
        } catch (e) {
          // Si no existe la imagen, usar imagen por defecto
          this.cardProvider = { name: provider["Marca de carro"], image: require('@/assets/tarjetas/Blanca.png') };
        }
      } else {
        this.cardProvider = { name: 'Proveedor Desconocido', image: require('@/assets/tarjetas/Blanca.png') };
      }
    },

    validateExpiryDate() {
      let value = this.payment.expiryDate.replace(/\D/g, ''); // Remover caracteres no numéricos
      if (value.length > 2) {
        value = value.slice(0, 2) + '/' + value.slice(2); // Insertar la barra después de los dos primeros dígitos
      }
      this.payment.expiryDate = value;

      const regex = /^(0[1-9]|1[0-2])\/(\d{2})$/; // Validar formato MM/AA
      const currentDate = new Date();
      const currentYear = currentDate.getFullYear() % 100; // Últimos dos dígitos del año actual
      const currentMonth = currentDate.getMonth() + 1; // Mes actual (0 indexado)

      if (!regex.test(value)) {
        this.errors.expiryDate = 'Formato inválido. Usa MM/AA.';
        return;
      }

      const [, month, year] = value.match(regex); // Extraer mes y año

      if (parseInt(year) < currentYear || (parseInt(year) === currentYear && parseInt(month) < currentMonth)) {
        this.errors.expiryDate = 'La tarjeta está vencida.';
      } else {
        this.errors.expiryDate = ''; // Válida
      }
    },


    validateCVV() {
      const regex = /^\d{3,4}$/; // CVV de 3 o 4 dígitos

      if (!regex.test(this.payment.cvv)) {
        this.errors.cvv = 'El CVV debe contener solo 3 o 4 dígitos numéricos.';
      } else {
        this.errors.cvv = ''; // Válido
      }
    },

  },
};
</script>

<style scoped>
/* Estilos opcionales */
</style>