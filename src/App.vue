<template>
  <div id="app">
    <h4>Basic: showing values</h4>
    <pure-vue-chart
      :points="dataPoints"
      :width="chartWidth"
      :height="chartHeight"
      :show-values="true"
    />
    <br>
    <br>
    <h4>Y axis</h4>
    <pure-vue-chart
      :show-y-axis="true"
      :points="dataPoints"
      :width="chartWidth"
      :height="chartHeight"
    />
    <br>
    <br>
    <h4>Max Y axis</h4>
    <pure-vue-chart
      :max-y-axis="50"
      :show-y-axis="true"
      :points="dataPoints"
      :width="chartWidth"
      :height="chartHeight"
    />
    <br>
    <br>
    <h4>X & Y axis</h4>
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
    <h4>Trend line</h4>
    <pure-vue-chart
      :show-y-axis="true"
      :show-x-axis="true"
      :points="dataPoints"
      :width="chartWidth"
      :height="chartHeight"
      :use-month-labels="true"
      :show-trend-line="true"
      :trend-line-width="2"
      trend-line-color="lightblue"
    />
    <br>
    <br>
    <h4>Color</h4>
    <pure-vue-chart
      :show-y-axis="false"
      :show-x-axis="true"
      :points="dataPoints"
      :width="chartWidth"
      :height="chartHeight"
      :show-values="true"
      :use-month-labels="true"
      :animation-duration="2"
      :months="['Jan', 'Fev', 'Mar', 'Abr', 'Mai', 'Jun', 'Jul', 'Ago', 'Set', 'Out', 'Nov', 'Dez']"
      barColor="lightpink"
    />
    <br>
    <br>
    <h4>Custom labels and titles</h4>
    <pure-vue-chart
      :show-y-axis="false"
      :show-x-axis="true"
      :points="dataPoints"
      :width="chartWidth"
      :height="chartHeight"
      :show-values="true"
    >
      <template v-slot:label="{ barIndex, label }" >
        <tspan v-if="barIndex === 0">custom</tspan>
        <tspan v-if="barIndex === 1">labels</tspan>
        <tspan v-if="barIndex === 2">&#128526;</tspan>
        <tspan v-if="barIndex > 2">{{ label }}</tspan>
      </template>
      <template v-slot:title="{ barIndex, staticValue }">
        <tspan>
          {{ barIndex === 0 ? `Custom title: ${staticValue}` : staticValue }}
        </tspan>
      </template>
    </pure-vue-chart>
    <br>
    <br>
    <h4>Plotting objects</h4>
    <pure-vue-chart
      :show-y-axis="false"
      :show-x-axis="true"
      :points="dataPointObjects"
      :width="chartWidth"
      :height="chartHeight"
      :show-values="true"
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
      dataPoints: [41.1, 1, 15, 16, 23, 41.1, 4, 8, 15, 22, 1, 12],
      dataPointObjects: [{label: 'N', value: 41.1}, {label: 'NW', value: 1}, {label: 'W', value: 15}, {label: 'SW', value: 16}, {label: 'S', value: 23}, {label: 'SE', value: 41.1}, {label: 'E', value: 4}, {label: 'NE', value: 8}],
      chartWidth: 450,
      chartHeight: 200,
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
