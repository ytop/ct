<template>
  <el-container>
    <el-main class="map-container">
      <div class="controls">
        <el-button-group>
          <el-button size="small" @click="handleZoomIn">
            <i class="el-icon-plus"></i>
          </el-button>
          <el-button size="small" @click="handleZoomOut">
            <i class="el-icon-minus"></i>
          </el-button>
          <el-button size="small" @click="handleReset">
            <i class="el-icon-refresh"></i>
          </el-button>
        </el-button-group>
      </div>
      <div ref="chartContainer" class="chart-container"></div>
      <div class="map-background" :style="mapBackgroundStyle"></div>
    </el-main>
  </el-container>
</template>

<script>
import worldGeoJson from '@/assets/world.json'

export default {
  name: 'WorldMap',
  data() {
    return {
      chart: null,
      mapData: [
        [20.2358421078053, 30.70913833075736, 100],
        [121.3415644319939, 31.9672194986475, 30],
        [116.0329284196291, 39.6141808346214, 80],
        [113.03790632245, 28.1985153835008, 61],
        [104.98925630313, 35.3839988649537, 70],
        [-108.62251255796, 26.6708486282843, 81]
      ],
      mapBackgroundStyle: {
        backgroundImage: 'url(' + require('@/assets/world-map-empty.png') + ')',
        backgroundSize: 'cover',
        backgroundPosition: 'center'
      }
    }
  },
  mounted() {
    this.initChart()
    window.addEventListener('resize', this.handleResize)
  },
  beforeDestroy() {
    window.removeEventListener('resize', this.handleResize)
    this.chart && this.chart.dispose()
  },
  methods: {
    async initChart() {
      // Register the world map data with ECharts
      this.$echarts.registerMap('world', worldGeoJson)
      
      this.chart = this.$echarts.init(this.$refs.chartContainer)
      
      const option = {
        backgroundColor: 'transparent',
        tooltip: {
          trigger: 'item',
          formatter: ({name, value}) => {
            if (Array.isArray(value)) {
              return `${name}<br/>Value: ${value[2]}`
            }
            return name
          }
        },
        geo: {
          map: 'world',
          roam: true,
          silent: true,
          zoom: 1.2,
          itemStyle: {
            borderColor: 'rgba(0, 0, 0, 0.2)',
            areaColor: 'transparent'
          },
          emphasis: {
            itemStyle: {
              areaColor: 'rgba(255, 255, 255, 0.1)'
            }
          }
        },
        series: [{
          type: 'custom',
          coordinateSystem: 'geo',
          geoIndex: 0,
          zlevel: 1,
          data: this.mapData,
          renderItem(params, api) {
            const coord = api.coord([
              api.value(0, params.dataIndex),
              api.value(1, params.dataIndex)
            ])
            
            if (!coord) {
              return
            }

            const coordinatorValue = api.value(2, params.dataIndex);
            const minCoordinator = 0;
            const maxCoordinator = 100;
            const normalizedValue = Math.min(1, Math.max(0, (coordinatorValue - minCoordinator) / (maxCoordinator - minCoordinator)));

            const r = Math.round(255 * (1 - normalizedValue));
            const b = Math.round(255 * normalizedValue);
            const color = `rgb(${r}, 0, ${b})`;

            const circles = []
            for (let i = 0; i < 5; i++) {
              circles.push({
                type: 'circle',
                shape: {
                  cx: 0,
                  cy: 0,
                  r: 30
                },
                style: {
                  stroke: color,
                  fill: 'none',
                  lineWidth: 2
                },
                keyframeAnimation: {
                  duration: 4000,
                  loop: true,
                  delay: (-i / 4) * 4000,
                  keyframes: [
                    {
                      percent: 0,
                      scaleX: 0,
                      scaleY: 0,
                      style: {
                        opacity: 1
                      }
                    },
                    {
                      percent: 1,
                      scaleX: 1,
                      scaleY: 0.4,
                      style: {
                        opacity: 0
                      }
                    }
                  ]
                }
              })
            }

            return {
              type: 'group',
              x: coord[0],
              y: coord[1],
              children: [
                ...circles,
                {
                  type: 'path',
                  shape: {
                    d: 'M16 0c-5.523 0-10 4.477-10 10 0 10 10 22 10 22s10-12 10-22c0-5.523-4.477-10-10-10zM16 16c-3.314 0-6-2.686-6-6s2.686-6 6-6 6 2.686 6 6-2.686 6-6 6z',
                    x: -10,
                    y: -35,
                    width: 20,
                    height: 40
                  },
                  style: {
                    fill: color
                  },
                  keyframeAnimation: {
                    duration: 1000,
                    loop: true,
                    delay: Math.random() * 1000,
                    keyframes: [
                      {
                        y: -10,
                        percent: 0.5,
                        easing: 'cubicOut'
                      },
                      {
                        y: 0,
                        percent: 1,
                        easing: 'bounceOut'
                      }
                    ]
                  }
                }
              ]
            }
          }
        }]
      }

      this.chart.setOption(option)
    },
    handleResize() {
      this.chart && this.chart.resize()
    },
    handleZoomIn() {
      const option = this.chart.getOption()
      const zoom = option.geo[0].zoom || 1
      option.geo[0].zoom = zoom * 1.2
      this.chart.setOption(option)
    },
    handleZoomOut() {
      const option = this.chart.getOption()
      const zoom = option.geo[0].zoom || 1
      option.geo[0].zoom = zoom * 0.8
      this.chart.setOption(option)
    },
    handleReset() {
      const option = this.chart.getOption()
      option.geo[0].zoom = 1
      option.geo[0].center = undefined
      this.chart.setOption(option)
    }
  }
}
</script>

<style scoped>
.map-container {
  width: 100%;
  height: 100vh;
  position: relative;
  overflow: hidden;
}

.chart-container {
  width: 100%;
  height: 100%;
  position: relative;
  z-index: 2;
}

.map-background {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  z-index: 1;
}

.controls {
  position: absolute;
  top: 20px;
  right: 20px;
  z-index: 1000;
  background: rgba(255, 255, 255, 0.9);
  padding: 20px;
  border-radius: 4px;
  box-shadow: 0 2px 12px 0 rgba(0, 0, 0, 0.1);
}
</style>