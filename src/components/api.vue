<template>
  <div class="container">
    <div v-if="isLoading" class="loading-container">
      <div class="loading-spinner"></div>
      <p>Espere un momento...</p>
    </div>

    <div v-else>
      <h2 class="section-title">Estado de Evaluaciones</h2>

      <!-- Contenedor para el resultado de la primera API -->
      <div v-if="info.result1" class="api-container">
        <h3 class="api-title">Estado de Evaluación del Tutor 2023</h3>
        <div v-for="(value, key) in info.result1" :key="key" class="result-box">

          
          <template v-if="typeof value !== 'object'">
            <div class="key">{{ key }}:</div>
            <div class="value">{{ displayValue(key, value) !== null ? displayValue(key, value) : '' }}</div>
          </template>

          <template v-else>
            <div class="key">{{ key }}:</div>
            <div v-for="(innerValue, innerKey) in value" :key="innerKey" class="inner-result-box">
              <div class="inner-key">{{ innerKey }}:</div>
              <div class="inner-value">{{ displayValue(innerKey, innerValue) !== null ? displayValue(innerKey, innerValue) : '' }}</div>
            </div>
          </template>
        </div>
      </div>

      <!-- Contenedor para el resultado de la segunda API -->
      <div v-if="info.result2" class="api-container api-container2">
        <h3 class="api-title">Estado de Evaluación por Ingeniería</h3>
        <div v-for="(value, key) in info.result2" :key="key" class="result-box">
          <template v-if="typeof value !== 'object'">
            <div class="key">{{ key }}:</div>
            <div class="value">{{ displayValue(key, value) !== null ? displayValue(key, value) : '' }}</div>
          </template>

          <template v-else>
            <div class="key">{{ key }}:</div>
            <div v-for="(innerValue, innerKey) in value" :key="innerKey" class="inner-result-box">
              <div class="inner-key">{{ innerKey }}:</div>
              <div class="inner-value">{{ displayValue(innerKey, innerValue) !== null ? displayValue(innerKey, innerValue) : '' }}</div>
            </div>
            <div class="evaluation-percentage">
              <div class="value">
                <span class="font-semibold">Porcentaje de alumnos faltantes:</span> {{ calculatePercentage(value) }}%
              </div>
            </div>
          </template>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.container {
  max-width: 300px;
  margin: 0 auto;
}

.loading-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  padding: 5px;
  background-color: #f1f1f1;
  border-radius: 8px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.loading-spinner {
  border: 4px solid #3498db;
  border-radius: 50%;
  border-top: 4px solid #fff;
  width: 30px;
  height: 30px;
  animation: spin 1s linear infinite;
  margin-bottom: 10px;
}

@keyframes spin {
  0% { transform: rotate(0deg); }
  100% { transform: rotate(360deg); }
}

.section-title {
  font-size: 1.8em;
  margin-bottom: 30px;
  color: #333;
}

.api-container {
  border: 1px solid #7D5413;
  border-radius: 8px;
  padding: 20px;
  margin-bottom: 20px;
  background-color: #7D5413;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
}

.api-container2 {
  border: 1px solid #075211;
  border-radius: 8px;
  padding: 20px;
  margin-bottom: 20px;
  background-color: #075211; /* Cambiado el color de fondo */
  box-shadow: 0 4px 8px rgba(0, 0, 100, 0.1);
}

.api-title {
  font-size: 1.5em;
  margin-bottom: 15px;
  color: #fbfdff;
}

.result-box {
  border: 1px solid #eaffc2;
  border-radius: 20px;
  padding: 8px;
  margin-bottom: 8px;
  background-color: #eaffc2;
}

.key {
  font-weight: bold;
  margin-bottom: 5px;
  color: #120101;
}

.value {
  color: #0f0101;
}

.inner-result-box {
  border: 1px solid #cff6c8;
  border-radius: 20px;
  padding: 8px;
  margin-top: 8px;
  background-color: #cff6c8;
}

.inner-key {
  font-weight: bold;
  margin-bottom: 3px;
  color: #0e0202;
}

.inner-value {
  color: #0a0101;
}
</style>

<script setup>
import { onMounted, ref } from 'vue';

const info = ref({
  result1: null,
  result2: null,
});
const isLoading = ref(true);

async function fetchData() {
  try {
    const response = await fetch('https://sitmotul.com.mx/api/statusEval');
    const data = await response.json();
    info.value.result1 = data;

    const response2 = await fetch('https://sitmotul.com.mx/api/statusEvalIng');
    const data2 = await response2.json();
    info.value.result2 = data2;

    isLoading.value = false;
    console.log(info.value);
  } catch (error) {
    console.error('Error al obtener datos:', error);
    isLoading.value = false;
  }
}

onMounted(() => {
  fetchData();
});

function displayValue(key, value) {
  if (key === 'inicio' || key === 'fin') {
    return null;  // Devuelve null para omitir estos valores
  } else {
    return value;
  }
}

function calculatePercentage(category) {
  // Calcular el porcentaje para la categoría actual
  const totalListas = category.listas || 0;
  const totalFaltantes = category.faltantes || 0;

  return totalListas > 0 ? Math.round((100 * totalFaltantes) / totalListas ) : 0;
}
</script>

