<template>
  <button class="btn btn-primary" @click="handleClick">
    Acceder
  </button>
</template>

<script>
export default {
  name: 'BtnLogin',
  methods: {
    handleClick() {
      // Emitir un evento para abrir el modal
      this.$emit('login'); 
    }
  }
}
</script>

<style scoped>
/* Se eliminan estilos personalizados */
</style>
