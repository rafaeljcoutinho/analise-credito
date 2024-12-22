<template>
  <div style="height: 150px;"></div>
  <credito-form></credito-form>
</template>

<script>
import CreditoForm from './pages/CreditoForm.vue';

export default {
  name: 'App',
  components: {
    CreditoForm,
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
