<template>
  <div v-if="cotizacion">
    <label for="pesos">Ingresá el valor en pesos:</label>
    <input
      id="pesos"
      :value="pesos"
      @input="validarEntrada"
      placeholder="Ej: 1000"
      type="text"
      inputmode="numeric"
    />

    <p v-if="valorEnDolares > 0">
      Dólares blue: <strong>{{ valorEnDolares.toFixed(2) }}</strong>
    </p>
    <p>Cotización actual (venta): ${{ cotizacion.venta }}</p>
    <p>Última actualización: {{ ultimaActualizacion }}</p>
  </div>

  <div v-else>
    <p>Cargando cotización del dólar blue...</p>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "TransformadorDePesosADolarBlue",
  data() {
    return {
      pesos: "",
      cotizacion: null,
      ultimaActualizacion: "",
    };
  },
  computed: {
    valorEnDolares() {
      const valorNum = parseInt(this.pesos, 10);
      if (!valorNum || !this.cotizacion?.venta) return 0;
      return valorNum / this.cotizacion.venta;
    },
  },
  methods: {
    async obtenerCotizacion() {
      try {
        const response = await axios.get(
          "https://api.bluelytics.com.ar/v2/latest"
        );
        const blue = response.data.blue;

        this.cotizacion = {
          venta: blue.value_sell,
          compra: blue.value_buy,
          promedio: blue.value_avg,
        };

        this.ultimaActualizacion = new Date().toLocaleString();
      } catch (error) {
        console.error("Error al obtener la cotización:", error);
      }
    },
    validarEntrada(event) {
      let valor = event.target.value;
      // Quitar todo lo que no sea dígito
      valor = valor.replace(/[^0-9]/g, "");
      this.pesos = valor;
      event.target.value = valor;
    },
  },
  mounted() {
    this.obtenerCotizacion();
    this.interval = setInterval(this.obtenerCotizacion, 2000);
  },
  beforeUnmount() {
    clearInterval(this.interval);
  },
};
</script>

<style scoped>
input {
  width: 200px;
  padding: 6px;
  font-size: 1rem;
}
</style>