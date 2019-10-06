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
    <br>
    <br>
    <pure-vue-chart
      :max-y-axis="50"
      :show-y-axis="true"
      :show-x-axis="true"
      :points="dataPoints"
      :width="chartWidth"
      :height="chartHeight"
    />
    <br>
    <br>
    <pure-vue-chart
      :show-y-axis="true"
      :show-x-axis="true"
      :points="dataPoints"
      :width="chartWidth"
      :height="chartHeight"
      :use-month-labels="true"
    />
    <br>
    <br>
    <pure-vue-chart
      :show-y-axis="false"
      :show-x-axis="true"
      :points="dataPoints"
      :width="chartWidth"
      :height="chartHeight"
      :show-values="true"
      :use-month-labels="true"
      :months="['Jan', 'Fev', 'Mar', 'Abr', 'Mai', 'Jun', 'Jul', 'Ago', 'Set', 'Out', 'Nov', 'Dez']"
    />
    <br>
    <br>
    <pure-vue-chart
      :show-y-axis="false"
      :show-x-axis="true"
      :points="dataPointObjects"
      :width="chartWidth"
      :height="chartHeight"
      :show-values="true"
    />
    <br>
    <br>
    <pure-vue-chart
      :show-y-axis="false"
      :show-x-axis="true"
      :points="dataPointObjects"
      :width="chartWidth"
      :height="chartHeight"
      :show-values="true"
    >
      <template v-slot:label="barProps" >
        <tspan v-if="barProps.bar.index === 1">Here</tspan>
        <tspan v-else-if="barProps.bar.index === 2">are</tspan>
        <tspan v-else-if="barProps.bar.index === 3">custom</tspan>
        <tspan v-else-if="barProps.bar.index === 4">labels</tspan>
        <tspan v-else-if="barProps.bar.index === 5">&#128526;</tspan>
        <tspan v-else>{{ barProps.bar.index }}</tspan>
      </template>
    </pure-vue-chart>
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
      dataPoints: [41.1, 1, 15, 16, 23, 41.1, 4, 8, 15, 22, 1, 12],
      dataPointObjects: [{label: 'N', value: 41.1}, {label: 'NW', value: 1}, {label: 'W', value: 15}, {label: 'SW', value: 16}, {label: 'S', value: 23}, {label: 'SE', value: 41.1}, {label: 'E', value: 4}, {label: 'NE', value: 8}],
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
.hello {
  transform: rotate(30 20,40);
}
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
