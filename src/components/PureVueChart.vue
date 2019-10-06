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
          <title>{{ bar.valueNotInMotion }}</title>
          <rect
            :width="bar.partitionWidth - 2"
            :height="bar.height"
            :x="2"
            :y="bar.yOffset"
          />
          <text
            v-if="showValues.some(val => val === 'val')"
            :x="bar.midPoint"
            :y="bar.yOffset"
            :dy="valPos(bar.height, 'val')+'px'"
            text-anchor="middle"
          >{{ bar.valueNotInMotion }}</text>
          <text
            v-if="showValues.some(val => val === 'pct')"
            :x="bar.midPoint"
            :y="bar.yOffset"
            :dy="valPos(bar.height, 'pct')+'px'"
            text-anchor="middle"
          >here</text>
          <g v-if="showXAxis">
            <text
              :x="bar.midPoint"
              :y="`${innerChartHeight + 14}px`"
              text-anchor="middle"
            >{{ dataLabels[bar.index] }}</text>
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
import { TweenLite } from 'gsap/TweenLite'

export default {
  props: {
    title: { type: String, default: '' },
    points: { type: Array, default: () => [] },
    height: { type: Number, default: 100 },
    width: { type: Number, default: 300 },
    showYAxis: { type: Boolean, default: false },
    showXAxis: { type: Boolean, default: false },
    easeIn: { type: Boolean, default: true },
    showValues: { type: Array, default: () => [] },
    maxYAxis: { type: Number, default: 0 },
    useMonthLabels: { type: Boolean, default: false },
    months: { type: Array, default: () => ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec'] },
  },
  data() {
    return {
      staticDataPoints: [],
      tweenedDataPoints: [],
      extraTopHeightForYAxisLabel: 4,
      extraBottomHeightForYAxisLabel: 4,
      digitsUsedInYAxis: 0,
    }
  },
  computed: {
    usingObjectsForDataPoints() {
      return this.points.every(x => typeof x === 'object')
    },
    dataPoints() {
      return this.usingObjectsForDataPoints
        ? this.points.map(item => item.value)
        : this.points
    },
    dataLabels() {
      return this.points.map((point, i) => {
        if (this.useMonthLabels) {
          return this.months[i]
        }
        return this.usingObjectsForDataPoints
          ? point.label
          : i + 1
      })
    },
    yAxisWidth() {
      switch (this.digitsUsedInYAxis) {
        case 4:
          return 30
        case 3:
          return 25
        case 2:
          return 18
        default:
          return 13
      }
    },
    xAxisHeight() {
      return this.showYAxis
        ? 12
        : 12 + this.extraBottomHeightForYAxisLabel + this.extraTopHeightForYAxisLabel
    },
    fullSvgWidth() {
      return this.width
    },
    fullSvgHeight() {
      return this.height
    },
    innerChartWidth() {
      return this.showYAxis
        ? this.width - this.yAxisWidth
        : this.width
    },
    innerChartHeight() {
      let chartHeight = this.height

      if (this.showYAxis) {
        chartHeight -= (this.extraTopHeightForYAxisLabel + this.extraBottomHeightForYAxisLabel)
      }
      if (this.showXAxis) {
        chartHeight -= this.xAxisHeight
      }
      return chartHeight
    },
    partitionWidth() {
      return this.innerChartWidth / this.dataPoints.length
    },
    maxDomain() {
      return this.maxYAxis ? this.maxYAxis : Math.ceil(Math.max(...this.dataPoints))
    },
    chartData() {
      return this.staticDataPoints.map((dataPoint, index) => ({
        valueNotInMotion: this.tweenedDataPoints[index],
        index,
        partitionWidth: this.partitionWidth,
        midPoint: this.partitionWidth / 2,
        x: index * this.partitionWidth,
        xMidpoint: index * this.partitionWidth + this.partitionWidth / 2,
        yOffset: this.innerChartHeight - this.y(dataPoint),
        height: this.y(dataPoint),
      }))
    },
  },
  watch: {
    points(updatedPoints) {
      this.tween(updatedPoints)
    },
  },
  created() {
    if (this.easeIn) {
      this.tween(this.dataPoints)
    } else {
      this.staticDataPoints = this.dataPoints
      this.tweenedDataPoints = this.dataPoints
    }
  },
  methods: {
    valPos(barHeight, type) {
      let position;
      switch(true) {
        case barHeight < 22:
          position = -5;
          break;
        default:
           position = 15;
      }
      return type = 'pct' && this.showValues.length > 1 ? (position - 5).toString() : position.toString()
    },
    y(val) {
      return (val / this.maxDomain) * this.innerChartHeight
    },
    tween(desiredDataArray) {
      const desiredData = {}
      const initialData = {}
      for (let i = 0; i < desiredDataArray.length; i += 1) {
        const key = i.toString()
        desiredData[key] = desiredDataArray[i]
        initialData[key] = this.staticDataPoints[i] || 0
      }
      const convertBackToArray = () => {
        const obj = Object.values(initialData)
        obj.pop()
        this.staticDataPoints = obj
      }
      TweenLite.to(initialData, 0.5, { ...desiredData, onUpdate: convertBackToArray })
      this.tweenedDataPoints = desiredDataArray
    },
    getTicks() {
      for (let i = 6; i > 0; i -= 1) {
        if (this.maxDomain % i === 0) {
          const shouldForceDecimals = i < 3
          const numberOfTicks = shouldForceDecimals ? 3 : i
          this.digitsUsedInYAxis = this.maxDomain
            .toFixed(shouldForceDecimals ? 1 : 0)
            .replace('.', '')
            .length
          return [...new Array(numberOfTicks + 1)].map((item, key) => {
            const tickValue = this.maxDomain / numberOfTicks * (numberOfTicks - key)
            const yCoord = this.innerChartHeight / numberOfTicks * key
            return {
              text: shouldForceDecimals ? tickValue.toFixed(1) : tickValue,
              x1: this.yAxisWidth - 4,
              y1: yCoord,
              x2: this.yAxisWidth - 1,
              y2: yCoord,
            }
          })
        }
      }
    },
  },
}
</script>

<style scoped lang="scss">
  .pure-vue-bar-chart rect {
    fill: deepskyblue
  }
  .pure-vue-bar-chart text {
    font: 10px sans-serif
  }
</style>
