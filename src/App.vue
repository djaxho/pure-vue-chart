<template>
  <div id="app">
    <pure-vue-chart
      :points="dataPoints"
      :width="chartWidth"
      :height="chartHeight"
      :show-values="true"
    />
    <br>
    <br>
    <pure-vue-chart
      :show-y-axis="true"
      :points="dataPoints"
      :width="chartWidth"
      :height="chartHeight"
    />
    <br>
    <br>
    <pure-vue-chart
      :max-y-axis="50"
      :show-y-axis="true"
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
      dataPoints: [41.1, 8, 15, 16, 23, 41.1, 4, 8, 15],
      chartWidth: 350,
      chartHeight: 120,
    };
  },
  created() {
    document.addEventListener('click', () => { this.changeData(); }, false);
  },
  methods: {
    changeData() {
      this.dataPoints = this.dataPoints.map(() => {
        return Math.floor(Math.random() * 41) + 1
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
