<script setup>
import { ref, onMounted, computed } from 'vue';

const props = defineProps({
  "countryData": Object,
  "codesData": Array,
});

const emit = defineEmits(['remove-code']);

async function removeCountry(code) {
  try {
    const response = await fetch(`http://localhost:3000/api/country/${code}`, {method: 'DELETE',});
    if(response.ok) {
      emit('remove-code', code);
      console.log('País eliminado');      
    } else {
      throw new Error('Error al eliminar el país');
    }
  } catch (error) {
    console.log('ERROR GRANDE: '+error);
  }
}
</script>

<template>
  <table v-if="countryData.name">
    <tr>
      <th>Nombre:</th>
      <td>{{ countryData.name }}</td>
      <th>Población</th>
      <td>{{ countryData.population }} millones</td>
    </tr>
    <tr>
      <th>PIB:</th>
      <td>{{ countryData.pib }} millones</td>
      <th>Área</th>
      <td>{{ countryData.area }} km<sup>2</sup></td>
    </tr>
    <tr>
      <th>Renta:</th>
      <td>{{ countryData.income }}</td>
      <th >PIB/habitante</th>
      <td :class="{warning: (countryData.pib / countryData.population) > countryData.income}">
        {{ (countryData.pib / countryData.population) }}
      </td>
    </tr>
    <button @click="removeCountry(countryData.code)">Eliminar</button>
  </table>
  <p v-else>
    No hay datos que mostrar
  </p>
</template>

<style scoped>
.warning {
  color:red;
}
</style>