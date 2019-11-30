<template>
  <svg
    class="pure-vue-bar-chart"
    :width="fullSvgWidth"
    :height="fullSvgHeight"
    aria-labelledby="title"
    role="img"
  >
    <title
      v-if="title"
      id="title"
    >{{ title }}</title>
    <g :transform="`translate(0,${showYAxis ? extraTopHeightForYAxisLabel : 0})`">
      <g
        :transform="`translate(${showYAxis ? yAxisWidth : 0},0)`"
        :width="innerChartWidth"
        :height="innerChartHeight"
      >
        <g
          v-for="bar in chartData"
          :key="bar.index"
          :transform="`translate(${bar.x},0)`"
        >
          <title>{{ bar.staticValue }}</title>
          <rect
            :width="bar.width"
            :height="bar.height"
            :x="2"
            :y="bar.yOffset"
          />
          <text
            v-if="showValues"
            :x="bar.midPoint"
            :y="bar.yOffset"
            :dy="`${bar.height < 22 ? '-5px' : '15px'}`"
            text-anchor="middle"
          >{{ bar.staticValue }}</text>
          <g v-if="showXAxis">
            <text
              :x="bar.midPoint"
              :y="`${innerChartHeight + 14}px`"
              text-anchor="middle"
            >
              <slot name='label' :bar="bar">{{ dataLabels[bar.index] }}</slot>
            </text>
            <line
              :x1="bar.midPoint"
              :x2="bar.midPoint"
              :y1="innerChartHeight+3"
              :y2="innerChartHeight"
              stroke="#555555"
              stroke-width="1"
            />
          </g>
        </g>

        <line
          v-if="showTrendLine"
          :x1="trendLine.x1"
          :y1="trendLine.y1"
          :x2="trendLine.x2"
          :y2="trendLine.y2"
          :stroke-width="trendLineWidth"
          :stroke="trendLineColor"
        />
      </g>
      <g v-if="showXAxis">
        <line
          :x1="showYAxis ? yAxisWidth-1 : 2"
          :x2="innerChartWidth + yAxisWidth"
          :y1="innerChartHeight"
          :y2="innerChartHeight"
          stroke="#555555"
          stroke-width="1"
        />
      </g>
      <g v-if="showYAxis">
        <line
          :x1="yAxisWidth-1"
          :x2="yAxisWidth-1"
          :y1="innerChartHeight"
          y2="0"
          stroke="#555555"
          stroke-width="1"
        />
        <g v-for="tick in getTicks()">
          <line
            :x1="tick.x1"
            :y1="tick.y1"
            :x2="tick.x2"
            :y2="tick.y2"
            stroke="#555555"
            stroke-width="1"
          />
          <text
            class="text-black"
            x="0"
            :y="tick.y1"
            alignment-baseline="central"
            fill="black"
          >{{ tick.text }}</text>
        </g>
      </g>
    </g>
  </svg>
</template>

<script>
import { TweenLite } from 'gsap/TweenLite';

export default {
  props: {
    title: { type: String, default: '' },
    points: { type: Array, default: () => [] },
    height: { type: Number, default: 100 },
    width: { type: Number, default: 300 },
    showYAxis: { type: Boolean, default: false },
    showXAxis: { type: Boolean, default: false },
    showTrendLine: { type: Boolean, default: false },
    trendLineColor: { type: String, default: 'green' },
    trendLineWidth: { type: Number, default: 2 },
    easeIn: { type: Boolean, default: true },
    showValues: { type: Boolean, default: false },
    maxYAxis: { type: Number, default: 0 },
    useMonthLabels: { type: Boolean, default: false },
  },
  data() {
    return {
      months: ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec'],
      dynamicPoints: [],
      staticPoints: [],
      extraTopHeightForYAxisLabel: 4,
      extraBottomHeightForYAxisLabel: 4,
      digitsUsedInYAxis: 0,
    };
  },
  computed: {
    usingObjectsForDataPoints() {
      return this.points.every(x => typeof x === 'object');
    },
    dataPoints() {
      return this.usingObjectsForDataPoints
        ? this.points.map(item => item.value)
        : this.points;
    },
    dataLabels() {
      return this.points.map((point, i) => {
        if (this.useMonthLabels) {
          return this.months[i];
        }
        return this.usingObjectsForDataPoints
          ? point.label
          : i + 1;
      });
    },
    yAxisWidth() {
      switch (this.digitsUsedInYAxis) {
        case 4:
          return 30;
        case 3:
          return 25;
        case 2:
          return 18;
        default:
          return 13;
      }
    },
    xAxisHeight() {
      return this.showYAxis
        ? 12
        : 12 + this.extraBottomHeightForYAxisLabel + this.extraTopHeightForYAxisLabel;
    },
    fullSvgWidth() {
      return this.width;
    },
    fullSvgHeight() {
      return this.height;
    },
    innerChartWidth() {
      return this.showYAxis
        ? this.width - this.yAxisWidth
        : this.width;
    },
    innerChartHeight() {
      let chartHeight = this.height;

      if (this.showYAxis) {
        chartHeight -= (this.extraTopHeightForYAxisLabel + this.extraBottomHeightForYAxisLabel);
      }
      if (this.showXAxis) {
        chartHeight -= this.xAxisHeight;
      }
      return chartHeight;
    },
    partitionWidth() {
      return this.innerChartWidth / this.dataPoints.length;
    },
    maxDomain() {
      return this.maxYAxis ? this.maxYAxis : Math.ceil(Math.max(...this.dataPoints));
    },
    chartData() {
      return this.dynamicPoints.map((dynamicValue, index) => ({
        staticValue: this.staticPoints[index],
        index,
        width: this.partitionWidth - 2,
        midPoint: this.partitionWidth / 2,
        x: index * this.partitionWidth,
        xMidpoint: index * this.partitionWidth + this.partitionWidth / 2,
        yOffset: this.innerChartHeight - this.y(dynamicValue),
        height: this.y(dynamicValue),
      }));
    },
    trendLine() {
      const slopeValues = this.applySlope(this.dynamicPoints);
      return {
        x1: this.partitionWidth / 2,
        y1: this.roundTo(this.innerChartHeight - this.y(slopeValues[0]), 2),
        x2: this.innerChartWidth - this.partitionWidth / 2,
        y2: this.roundTo(this.innerChartHeight - this.y(slopeValues[slopeValues.length - 1]), 2),
      };
    },
  },
  watch: {
    points(updatedPoints) {
      this.tween(this.dataPoints);
    },
  },
  created() {
    if (this.easeIn) {
      this.tween(this.dataPoints);
    } else {
      this.dynamicPoints = this.dataPoints;
      this.staticPoints = this.dataPoints;
    }
  },
  methods: {
    y(val) {
      return (val / this.maxDomain) * this.innerChartHeight;
    },
    roundTo(n, digits = 0) {
      let negative = false;
      let number = n;
      if (number < 0) {
        negative = true;
        number *= -1;
      }
      const multiplicator = Math.pow(10, digits);
      number = parseFloat((number * multiplicator).toFixed(11));
      number = (Math.round(number) / multiplicator).toFixed(2);
      if (negative) {
        number = (number * -1).toFixed(2);
      }
      return number;
    },
    tween(desiredDataArray) {
      const desiredData = {};
      const initialData = {};
      for (let i = 0; i < desiredDataArray.length; i += 1) {
        const key = i.toString();
        desiredData[key] = desiredDataArray[i];
        initialData[key] = this.dynamicPoints[i] || 0;
      }
      const convertBackToArray = () => {
        const obj = Object.values(initialData);
        obj.pop();
        this.dynamicPoints = obj;
      };
      TweenLite.to(initialData, 0.5, { ...desiredData, onUpdate: convertBackToArray });
      this.staticPoints = desiredDataArray;
    },
    getTicks() {
      for (let i = 6; i > 0; i -= 1) {
        if (this.maxDomain % i === 0) {
          const shouldForceDecimals = i < 3;
          const numberOfTicks = shouldForceDecimals ? 3 : i;
          this.digitsUsedInYAxis = this.maxDomain
            .toFixed(shouldForceDecimals ? 1 : 0)
            .replace('.', '')
            .length;
          return [...new Array(numberOfTicks + 1)].map((item, key) => {
            const tickValue = this.maxDomain / numberOfTicks * (numberOfTicks - key);
            const yCoord = this.innerChartHeight / numberOfTicks * key;
            return {
              text: shouldForceDecimals ? tickValue.toFixed(1) : tickValue,
              x1: this.yAxisWidth - 4,
              y1: yCoord,
              x2: this.yAxisWidth - 1,
              y2: yCoord,
            };
          });
        }
      }
    },
    applySlope(values) {
      let xAvg = 0;
      let yAvg = 0;
      for (let x = 0; x < values.length; x++) {
        xAvg += x;
        yAvg += values[x];
      }
      xAvg /= values.length;
      yAvg /= values.length;
      let v1 = 0; let
        v2 = 0;
      for (let x = 0; x < values.length; x++) {
        v1 += (x - xAvg) * (values[x] - yAvg);
        v2 += Math.pow(x - xAvg, 2);
      }
      const a = v1 / v2;
      const b = yAvg - a * xAvg;
      const result = [];
      for (let index = 0; index < values.length; index++) {
        result.push(a * index + b);
      }
      return result;
    },
  },
};
</script>

<style scoped lang="scss">
  .pure-vue-bar-chart rect {
    fill: deepskyblue
  }
  .pure-vue-bar-chart text {
    font: 10px sans-serif
  }
</style>
