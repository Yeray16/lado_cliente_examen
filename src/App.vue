<script setup>
  import { ref, onMounted, computed } from 'vue';
  import codes from './codes.json';
  import CountryData from './components/CountryData.vue';

  const codesData = ref([]);
  const selectedCodes = ref([]);
  const countryData = ref([]);
  const dataTypes = ['population', 'pib', 'area', 'income'];

  onMounted(() => {
    codesData.value = codes;

  });
  console.log(codesData.value);

  const name = ref('');

  function selectCode(code) {
    if(!selectedCodes.value.includes(code)) {
      getCountryInfo(code);
      console.log(selectedCodes.value);
    }
  }

  function removeCode(code) {
    selectedCodes.value = selectedCodes.value.filter(codeCountry => codeCountry!=code);
    countryData.value = {};
  }

  async function getCountryInfo (code) {
    try {
      const response = await fetch('http://localhost:3000/api/country/'+code);
      const data = await response.json();
      countryData.value = data;
      selectedCodes.value.push(code);
    } catch (error) {
      console.log('Hubo un error al buscar los datos: '+error);
      countryData.value = [];
    }
     
  }
</script>

<template>
  <div class="parent">
    <div class="header">
      <h1>Datos mundiales</h1>
    </div>
    <div class="name">
      <p>
        Inserta tu nombre: 
        <input type="text" v-model="name">
        <p v-if="name">
          Tu nombre <b>{{ name }}</b> tiene <b>{{ name.length }}</b> letras.
        </p>
      </p>
    </div>
    <div class="div-codes">
      <ul>
        <li v-for="code in codesData" @click="selectCode(code)">
          {{ code }}
        </li>
      </ul>
    </div>
    <div class="selected">
      <p v-if="selectedCodes.length>=1">
        <h2>Seleccionados ({{ selectedCodes.length }}):</h2>
        <ul>
          <li v-for="codeCountry in selectedCodes">
            {{ codeCountry }}
            <button @click="removeCode(codeCountry)">Desmarcar</button>
          </li>
        </ul>
      </p>
      <p v-else>
        Debes seleccionar algún país en el panel de códigos.
      </p>    
    </div>
    <div class="country-data">
      <CountryData :countryData="countryData"
                   @remove-code="removeCode"
      />
    </div>
    <div class="options">
      <p v-for="type in dataTypes">
        <input type="radio" :value="type" name="type">
        <label for="type">{{ type }}</label>
      </p>
    </div>
    <div class="chart"></div>
  </div>
</template>

<style scoped>
.parent {
  display: grid;
  grid-template-columns: repeat(5, 1fr);
  grid-template-rows: auto repeat(3, 1fr);
  grid-column-gap: 0px;
  grid-row-gap: 0px;
  height: 100vh;
  margin: -2rem;
}

.header {
  grid-area: 1 / 1 / 2 / 6;
  background-color: aquamarine;
}

.header h1 {
  margin: 2px;
}

.div-codes {
  grid-area: 2 / 1 / 6 / 2;
  background-color: azure;
  overflow-y: auto;
}

.name {
  grid-area: 2 / 2 / 3 / 3;
  background-color: antiquewhite;
}

.selected {
  grid-area: 2 / 5 / 4 / 6;
  background-color: lightyellow;
  overflow-y: auto;
}

.country-data {
  grid-area: 2 / 3 / 3 / 5;
  background-color: lightseagreen;
}

.options {
  grid-area: 3 / 2 / 4 / 5;
  background-color: bisque;
}

.chart {
  grid-area: 4 / 2 / 5 / 6;
  background-color: aqua;
}

ul li {
  text-align: left;
  font-size: 0.9rem;
}

ul li:hover {
  font-weight: bold;
  cursor: pointer
}

ul li button {
  font-size: 0.6rem;
  margin: 2px;
}

h2 {
  font-size: 1.2rem;
}
</style>
