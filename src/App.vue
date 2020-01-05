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
      :show-trend-line="true"
      :trend-line-width="2"
      trend-line-color="lightblue"
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
      :label-height="20"
      :points="dataPointObjects"
      :width="chartWidth"
      :height="chartHeight"
      :show-values="true"
    >
      <template v-slot:label="barProps">
        <text
          v-if="barProps.bar.index === 1"
          :x="barProps.bar.midPoint"
          :y="`${barProps.bar.yLabel + 10}px`"
          text-anchor="middle"
        >
          Here
        </text>
        <text
          v-else-if="barProps.bar.index === 2"
          :x="barProps.bar.midPoint"
          :y="`${barProps.bar.yLabel + 10}px`"
          text-anchor="middle"
        >
          are
        </text>
        <text
          v-else-if="barProps.bar.index === 3"
          :x="barProps.bar.midPoint"
          :y="`${barProps.bar.yLabel + 10}px`"
          text-anchor="middle"
        >
          custom
        </text>
        <text
          v-else-if="barProps.bar.index === 4"
          :x="barProps.bar.midPoint"
          :y="`${barProps.bar.yLabel + 10}px`"
          text-anchor="middle"
        >
          labels
        </text>
        <image
          v-else-if="barProps.bar.index === 5"
          :x="`${barProps.bar.midPoint - 10}px`"
          :y="`${barProps.bar.yLabel}px`"
          height="20"
          width="20"
          href="https://raw.githubusercontent.com/vuejs/art/master/logo.png"
        />
        <text
          v-else-if="barProps.bar.index === 6"
          :x="barProps.bar.midPoint"
          :y="`${barProps.bar.yLabel + 10}px`"
          text-anchor="middle"
        >
          &#128526;
        </text>
        <text
          v-else
          :x="barProps.bar.midPoint"
          :y="`${barProps.bar.yLabel + 10}px`"
          text-anchor="middle"
        >
          {{ barProps.bar.label }}
        </text>
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
