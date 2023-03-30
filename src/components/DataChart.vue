<template>
  <div>
    <canvas ref="chartCanvas"></canvas>
  </div>
</template>

<script>
import { ref, onMounted, watch } from 'vue';
import Chart from 'chart.js/auto';

export default {
  name: 'DataChart',
  props: {
    globalStats: {
      type: Array,
      required: true
    }
  },
  setup(props) {
    const chartCanvas = ref(null);
    let chartInstance = null;

    const createChart = () => {
      const ctx = chartCanvas.value.getContext('2d');
      chartInstance = new Chart(ctx, {
        type: 'line',
        data: {
          labels: ['No data available'],
          datasets: [
            {
              label: 'Confirmed Cases',
              data: [0],
              borderColor: 'blue',
              backgroundColor: 'blue',
              fill: false
            },
            {
              label: 'Deaths',
              data: [0],
              borderColor: 'red',
              backgroundColor: 'red',
              fill: false
            },
            {
              label: 'Recovered',
              data: [0],
              borderColor: 'green',
              backgroundColor: 'green',
              fill: false
            }
          ]
        },
        options: {
          responsive: true,
          maintainAspectRatio: false
        }
      });
    };

    const updateChart = () => {
      chartInstance.data.labels = props.globalStats.map((stat) => stat.Date);
      chartInstance.data.datasets[0].data = props.globalStats.map((stat) => stat.TotalConfirmed);
      chartInstance.data.datasets[1].data = props.globalStats.map((stat) => stat.TotalDeaths);
      chartInstance.data.datasets[2].data = props.globalStats.map((stat) => stat.TotalRecovered);
      chartInstance.update();
    };

    onMounted(() => {
      createChart();
    });

    watch(() => props.globalStats, () => {
      if (chartInstance) {
        updateChart();
      } else {
        createChart();
      }
    });

    return {
      chartCanvas
    };
  }
};
</script>
