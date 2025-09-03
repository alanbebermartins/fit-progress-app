<script setup>
import { ref, computed } from 'vue'
import exerciseData from '../assets/json/exercise_data.json'
import ChartView from './chartView.vue'

const selected = ref('')

const resultFiltered = computed(() => {
  if (!selected.value) return []

  return exerciseData
    .filter(e => e.exerciseName === selected.value)
    .map(e => {
      const totalVolumeWeight = e.unitWeight * (
        e.firstSet + e.secondSet + e.thirdSet + e.fourthSet
      )

      return {
        exerciseName: e.exerciseName,
        unitWeight: e.unitWeight,
        trainingType: e.trainingType,
        firstSet: e.firstSet,
        secondSet: e.secondSet,
        thirdSet: e.thirdSet,
        fourthSet: e.fourthSet,
        totalVolumeWeight // ✅ já vem calculado no item
      }
    })
})

</script>

<template>

  <div class="exercise__evolution-selector">
    <select class="custom-select" v-model="selected">
      <option value="" disabled>Selecione uma opção</option>
      <option v-for="(exercise, index) in exerciseData" :key="index" :value="exercise.exerciseName">
        {{ exercise.exerciseName }}
      </option>
    </select>
  </div>
  <div class="exercise__evolution-chart-main">
    <ChartView :chartData="resultFiltered"/>
  </div>
</template>

<style>
.exercise__evolution-selector {
  width: 100%;
  max-width: 1280px;
  padding: 40px 0;
}

.custom-select {
  width: 100%;
  max-width: 400px;
  padding: 10px 15px;
  font-size: 16px;
  border: 2px solid #ccc;
  border-radius: 8px;
  background-color: #fff; /* fundo branco */
  color: #333;            /* texto escuro */
  outline: none;
  cursor: pointer;
  appearance: none;
  -webkit-appearance: none;
  -moz-appearance: none;
  background-image: url("data:image/svg+xml;charset=US-ASCII,<svg xmlns='http://www.w3.org/2000/svg' width='12' height='12'><polygon points='0,0 12,0 6,6' fill='%23333'/></svg>");
  background-repeat: no-repeat;
  background-position: right 10px center;
  background-size: 12px;
}

.exercise__evolution .exercise__evolution-chart-main {
  width: 100%;
  height: 400px !important;
  max-height: 400px !important;
}

.exercise__evolution .exercise__evolution-chart-main canvas {
  margin: 0 auto;
}

</style>