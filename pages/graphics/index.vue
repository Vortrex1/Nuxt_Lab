<template>
  <div>
    <div class="graphic-container">
      <canvas id="chart"></canvas>
    </div>
  </div>
</template>

<script>
import { Bar } from 'vue-chartjs'

export default {
  extends: Bar,
  data() {
    return {
      chartData: {
        labels: ['ST Rating'],
        datasets: [
          {
            label: 'Score',
            backgroundColor: '#54d3c6',
            data: [80],
          },
        ],
      },
      options: {
        responsive: true,
        maintainAspectRatio: false,
        scales: {
          y: {
            beginAtZero: true,
            grid: {
              display: false,
            },
          },
          x: {
            grid: {
              display: false,
            },
          },
        },
      },
    }
  },
  mounted() {
    this.renderChart(this.chartData, this.options)
  },
}
</script>

<style scoped>
.graphic-container {
  width: 400px;
  height: 200px;
  margin: 0 auto;
  display: flex;
  justify-content: center;
  align-items: center;
}

canvas {
  width: 100%;
  height: auto;
}
</style>