<template>
  <nav class="navbar navbar-expand-lg navbar-light bg-white px-4">
    <div class="container-fluid d-flex align-items-center">
      <a class="navbar-brand me-auto" href="#">
        <img alt="logo_Andapaya" src="@/assets/Logo_Andapaya.png" height="40" />
      </a>
      <div class="d-flex justify-content-center flex-grow-1 gap-3">
        <BtnReserva />
      </div>
      <div class="d-flex align-items-center ms-auto gap-3">
        <div class="dropdown">
          <a class="nav-link dropdown-toggle" href="#" id="currencyDropdown" role="button" data-bs-toggle="dropdown"
            aria-expanded="false">
            {{ selectedCurrency.code }} {{ selectedCurrency.symbol }} | {{ selectedLanguage }}
          </a>
          <ul class="dropdown-menu" aria-labelledby="currencyDropdown">
            <li class="dropdown-header">Languaje</li>
            <li v-for="(lang, index) in languages" :key="'lang-' + index">
              <a class="dropdown-item" href="#" @click="selectLanguage(lang)">
                <img :src="lang.flag" alt="flag" width="20" height="15" class="me-1" />
                {{ lang.name }}
              </a>
            </li>
            <li>
              <hr class="dropdown-divider" />
            </li>
            <li class="dropdown-header">Moneda</li>
            <li v-for="(currency, index) in currencies" :key="'currency-' + index">
              <a class="dropdown-item" href="#" @click="selectCurrency(currency)">
                <img :src="currency.flag" alt="flag" width="20" height="15" class="me-1" />
                {{ currency.code }} {{ currency.symbol }}
              </a>
            </li>
          </ul>
        </div>
        <BtnLogin @login="handleLogin" />
      </div>
    </div>
  </nav>
</template>

<script>
import BtnReserva from './BtnReserva.vue';
import BtnLogin from './BtnLogin.vue';

export default {
  name: 'HeaderComponent',
  components: {
    BtnReserva,
    BtnLogin,
  },
  data() {
    return {
      languages: [
        { name: "Español", code: "ES", flag: require('@/assets/Banderas/espana.png') },
        { name: "English", code: "EN", flag: require('@/assets/Banderas/estados-unidos.png') },
        { name: "Portugues", code: "BR", flag: require('@/assets/Banderas/brasil.png') }
      ],
      currencies: [
        { name: "Peso Argentino", code: "ARS", symbol: "$", flag: require('@/assets/Banderas/argentina.png') },
        { name: "Dólar Estadounidense", code: "USD", symbol: "$", flag: require('@/assets/Banderas/estados-unidos.png') },
        { name: "Euro", code: "EUR", symbol: "€", flag: require('@/assets/Banderas/union-europea.png') }
      ],
      selectedLanguage: "ES",
      selectedCurrency: { code: "ARS", symbol: "$", flag: require('@/assets/Banderas/argentina.png') }
    };
  },
  methods: {
    handleLogin() {
      // Emitimos un evento cuando se hace clic en "Acceder"
      this.$emit('login');
    },

    // Selección de idioma
    selectLanguage(lang) {
      this.selectedLanguage = lang.code;
    },
    // Selección de moneda
    selectCurrency(currency) {
      this.selectedCurrency = currency;
    }
  },
};
</script>

<style scoped>
/* Aquí puedes agregar tus estilos si es necesario */
</style>
