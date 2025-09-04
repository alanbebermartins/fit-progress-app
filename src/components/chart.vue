<script setup>
  import { ref, computed } from 'vue'
  import exerciseData from '../assets/json/exercise_data.json'
  import erxercisesListNames from '../assets/json/exercises_list_names.json'
  import measureListUnits from '../assets/json/measure_units.json'
  import ChartView from './chartView.vue'

  const selectedExercise = ref('')
  const selectedMeasure = ref('')
  const resultFiltered = ref([])

function isValidNumber(value) {
  return value !== '' && !isNaN(value) && Number(value) > 0
}

function clearFilter() {
  selectedExercise.value = ''
  selectedMeasure.value = ''
  resultFiltered.value = []
}

function searchFilter() {
  // Só busca se os dois selects tiverem valor
  if (!selectedExercise.value || !selectedMeasure.value) {
    resultFiltered.value = []
    return
  }

  resultFiltered.value = exerciseData
    .filter(e => 
      e.exerciseName === selectedExercise.value &&
      e.measure === selectedMeasure.value
    )
    .map(e => {
      let totalVolumeWeight = 0
      if (
        isValidNumber(e.unitWeight) &&
        isValidNumber(e.firstSet) &&
        isValidNumber(e.secondSet) &&
        isValidNumber(e.thirdSet) &&
        isValidNumber(e.fourthSet)
      ) {
        totalVolumeWeight = Number(e.unitWeight) * (
          Number(e.firstSet) +
          Number(e.secondSet) +
          Number(e.thirdSet) +
          Number(e.fourthSet)
        )
      }

      return {
        exerciseName: e.exerciseName,
        unitWeight: e.unitWeight,
        trainingType: e.trainingType,
        measure: e.measure,
        firstSet: e.firstSet,
        secondSet: e.secondSet,
        thirdSet: e.thirdSet,
        fourthSet: e.fourthSet,
        totalVolumeWeight
      }
    }
  )
}

</script>

<template>
  <div class="exercise__evolution-selector">
    <!-- Select de Exercício -->
    <select class="custom-select" v-model="selectedExercise">
      <option value="" disabled>Selecione um Exercício</option>
      <option  v-for="(exerciseItemName, index) in erxercisesListNames" :key="index" :value="exerciseItemName.exerciseName">
        {{ exerciseItemName.exerciseName }}
      </option>
    </select>

    <!-- Select de Unidade de Medida -->
    <select class="custom-select" v-model="selectedMeasure">
      <option value="" disabled>Selecione uma Unidade de Medida</option>
      <option  v-for="(measureItemUnit, index) in measureListUnits" :key="index" :value="measureItemUnit.measure">
        {{ measureItemUnit.measure }}
      </option>
    </select>

    <button @click="clearFilter" class="clear__button type__button">Limpar</button>
    
    <button @click="searchFilter" class="search__button type__button">Filtrar</button>
  </div>

  <div class="exercise__evolution-chart-main">
    <ChartView :chartData="resultFiltered" />
  </div>
</template>


<style>
.exercise__evolution-selector {
  width: 100%;
  max-width: 1280px;
  margin: 0 auto;
  padding: 40px 0;
  display: flex;
  flex-wrap: wrap;
  gap: 16px;
  align-items: center;
  justify-content: center;
}

.custom-select {
  width: 100%;
  max-width: 300px;
  padding: 10px 15px;
  font-size: 14px;
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

.custom-select option {
  font-size: 14px;
}

.exercise__evolution-selector .type__button {
  font-size: 16px;
  color: #FFF;
  border-radius: 8px;
  border: 1px solid #fff;
  min-width: 100px;
}

.exercise__evolution-selector .type__button.clear__button {
  background-color: rgb(224, 49, 49);
}

.exercise__evolution-selector .type__button.search__button {
  background-color: rgb(17, 20, 173);
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