<template>
  <canvas  ref="canvas" style="width:100%; height:400px; background-color: #FFFFFF;"></canvas>
</template>

<script setup>
import { ref, onMounted, watch, nextTick } from 'vue'
import { Chart, LineController, LineElement, PointElement, LinearScale, CategoryScale, Legend, Title } from 'chart.js'

// Registrar componentes necessários do Chart.js
Chart.register(LineController, LineElement, PointElement, LinearScale, CategoryScale, Legend, Title)

const props = defineProps({
  chartData: {
    type: Array,
    default: () => []
  }
})

const canvas = ref(null)
let chartInstance = null

function renderChart() {
  if (!canvas.value || !props.chartData.length) return

  const labels = props.chartData.map(item => item.exerciseName)
  const dataValues = props.chartData.map(item => item.totalVolumeWeight)

  const data = {
    labels,
    datasets: [{
      label: 'Volume Total',
      data: dataValues,
      borderColor: 'rgb(75, 192, 192)',
      fill: false,
      tension: 0.1
    }]
  }

  const config = {
    type: 'line',
    data,
    options: {
      responsive: true,
      maintainAspectRatio: false,
      plugins: {
        legend: { display: true },
        title: { display: true, text: 'Evolução do Exercício' }
      },
      scales: {
        y: { beginAtZero: true }
        }
    }
  }

  if (chartInstance) chartInstance.destroy()
  chartInstance = new Chart(canvas.value, config)
}

onMounted(() => nextTick(() => renderChart()))

watch(
  () => props.chartData,
  (newData) => {
    nextTick(() => {
      if (!newData.length) {
        // Se não houver dados, destrói o gráfico e sai
        if (chartInstance) {
          chartInstance.destroy()
          chartInstance = null
        }
        return
      }
      // Se houver dados, renderiza o gráfico
      renderChart()
    })
  },
  { deep: true } // opcional, mas útil se você quiser observar mudanças internas no array
)

</script>
