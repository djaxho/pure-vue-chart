<template>
  <div>
    <svg
      class="chart"
      :width="width"
      :height="height"
      aria-labelledby="title"
      role="img"
    >
      <title
        v-if="title"
        id="title"
      >{{ title }}</title>
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
          v-if="showValues"
          :x="bar.midPoint"
          :y="bar.yOffset"
          dy="20px"
          text-anchor="middle"
        >{{ bar.valueNotInMotion }}</text>
      </g>
    </svg>
  </div>
</template>

<script>
import { TweenLite } from 'gsap/TweenLite'

export default {
  props: {
    title: { type: String, default: '' },
    points: { type: Array, default: () => [] },
    height: { type: Number, default: 100 },
    width: { type: Number, default: 300 },
    easeIn: { type: Boolean, default: true },
    showValues: { type: Boolean, default: false },
  },
  data() {
    return {
      staticDataPoints: [],
      tweenedDataPoints: [],
    }
  },
  computed: {
    chartWidth() {
      return this.width
    },
    chartHeight() {
      return this.height
    },
    partitionWidth() {
      return this.chartWidth / this.points.length
    },
    maxDomain() {
      return Math.max(...this.points)
    },
    chartData() {
      return this.staticDataPoints.map((dataPoint, index) => {
        return {
          valueNotInMotion: this.tweenedDataPoints[index],
          index,
          partitionWidth: this.partitionWidth,
          midPoint: this.partitionWidth / 2,
          x: index * this.partitionWidth,
          xMidpoint: index * this.partitionWidth + this.partitionWidth / 2,
          yOffset: this.chartHeight - this.y(dataPoint),
          height: this.y(dataPoint),
        }
      })
    },
  },
  watch: {
    points(updatedPoints) {
      this.tween(updatedPoints)
    },
  },
  created() {
    if (this.easeIn) {
      this.tween(this.points)
    } else {
      this.staticDataPoints = this.points
      this.tweenedDataPoints = this.points
    }
  },
  methods: {
    y(val) {
      return (val / this.maxDomain) * this.chartHeight
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
  },
}
</script>

<style scoped lang="scss">
    .chart rect {
        fill: deepskyblue;
    }
    .chart text {
        font: 12px sans-serif;
    }
</style>
