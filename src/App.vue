<template>
  <div id="app">
    <pure-vue-chart
      :points="dataPoints"
      :width="chartWidth"
      :height="chartHeight"
    />
  </div>
</template>

<script>
import PureVueChart from './components/PureVueChart.vue';

export default {
  name: 'App',
  components: {
    PureVueChart,
  },
  data() {
    return {
      dataPoints: [42, 8, 15, 16, 23, 42, 4, 8, 15],
      chartWidth: 600,
      chartHeight: 200,
    };
  },
  created() {
    document.addEventListener('click', () => { this.changeData(); }, false);
  },
  methods: {
    changeData() {
      this.dataPoints = this.dataPoints.map(() => {
        return Math.floor(Math.random() * Math.max(...this.dataPoints)) + 1
      });
    },
  },
};
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
