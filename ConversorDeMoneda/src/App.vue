<template>
  <div id="app">
    <h1>Conversor de Pesos a Dólares</h1>
    <div class="input-group">
      <label for="pesos">Valor en Pesos:</label>
      <input id="pesos" v-model.number="pesos" type="number" placeholder="Ingresar valor en pesos">
    </div>
    <div class="input-group">
      <label for="cotizacion">Cotización del Dólar:</label>
      <input id="cotizacion" v-model.number="cotizacion" type="number" placeholder="Ingresar cotización del dólar" :disabled="autoUpdate">
    </div>
    <div class="result">
      <p>Dólares: {{ conversion }}</p>
    </div>
    <div class="checkbox-group">
      <label>
        <input type="checkbox" v-model="autoUpdate"> Actualización automática
      </label>
    </div>
    <div class="update-info" v-if="autoUpdate">
      <p>Última actualización: {{ lastUpdate }}</p>
    </div>
    <p class="responses">respuestas: 1:b  2:b  3:c  4:b  5:c</p>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      pesos: 0,
      cotizacion: 0,
      autoUpdate: false,
      lastUpdate: null
    }
  },
  computed: {
    conversion() {
      return (this.pesos / this.cotizacion).toFixed(2);
    }
  },
  watch: {
    autoUpdate(val) {
      if (val) {
        this.startAutoUpdate();
      } else {
        this.stopAutoUpdate();
      }
    }
  },
  methods: {
    async fetchCotizacion() {
      try {
        const response = await axios.get('https://api.bluelytics.com.ar/v2/latest');
        this.cotizacion = response.data.blue.value_sell;
        this.lastUpdate = new Date().toLocaleString();
      } catch (error) {
        console.error('Error fetching cotización:', error);
      }
    },
    startAutoUpdate() {
      this.fetchCotizacion();
      this.updateInterval = setInterval(this.fetchCotizacion, 2000);
    },
    stopAutoUpdate() {
      clearInterval(this.updateInterval);
    }
  },
  beforeUnmount() {
    this.stopAutoUpdate();
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

h1 {
  color: #42b983;
}

.input-group {
  margin: 20px 0;
}

.input-group label {
  display: block;
  margin-bottom: 5px;
  font-weight: bold;
}

.input-group input {
  padding: 10px;
  width: 300px;
  max-width: 100%;
  box-sizing: border-box;
}

.result {
  margin: 20px 0;
}

.result p {
  font-size: 1.5em;
  color: #333;
}

.checkbox-group {
  margin: 20px 0;
}

.update-info {
  margin: 20px 0;
  font-style: italic;
}

.responses {
  margin-top: 40px;
  font-size: 0.9em;
  color: #666;
}
</style>
