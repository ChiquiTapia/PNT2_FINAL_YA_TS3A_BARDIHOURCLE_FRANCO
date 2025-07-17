<template>
  <div id="app">
    <h1>Convertidor de Pesos a Dólar Blue</h1>
    <TransformadorDePesosADolarBlue />
  </div>
</template>

<script>
import TransformadorDePesosADolarBlue from './components/TransformadorDePesosADolarBlue.vue'

export default {
  name: 'App',
  components: {
    TransformadorDePesosADolarBlue
  }
}
</script>