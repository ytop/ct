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
      timelineData: [
        {
          title: 'Helpdesk tickets',
          timestamp: '2024-03-20 09:00:00',
          status: 'successful',
          type: 'success',
          description: 'All tickets processed successfully'
        },
        {
          title: 'CLD Payments Input Error',
          timestamp: '2024-03-20 09:30:00',
          status: 'failed',
          type: 'danger',
          description: 'Invalid payment format detected in batch processing'
        },
        {
          title: 'CLD Payments Authorized Payment',
          timestamp: '2024-03-20 10:00:00',
          status: 'successful',
          type: 'success',
          description: 'All payments authorized successfully'
        },
        {
          title: 'CLD Payments Time Critical Payment Failure',
          timestamp: '2024-03-20 10:30:00',
          status: 'failed',
          type: 'danger',
          description: 'System timeout during critical payment processing'
        },
        {
          title: 'CLD Payments STP Rate',
          timestamp: '2024-03-20 11:00:00',
          status: 'successful',
          type: 'success',
          description: 'STP rate achieved: 98.5%'
        },
        {
          title: 'CLD Payments Reconsiliation',
          timestamp: '2024-03-20 11:30:00',
          status: 'successful',
          type: 'success',
          description: 'Reconciliation completed with no discrepancies'
        },
        {
          title: 'OSD Custody Recondiliation',
          timestamp: '2024-03-20 12:00:00',
          status: 'not_started',
          type: 'info',
          description: 'Scheduled for afternoon batch'
        },
        {
          title: 'OSD Custody Erros in Client Cash statement',
          timestamp: '2024-03-20 12:30:00',
          status: 'not_started',
          type: 'info',
          description: 'Pending verification'
        },
        {
          title: 'FID Client Onboarding',
          timestamp: '2024-03-20 13:00:00',
          status: 'successful',
          type: 'success',
          description: '5 new clients onboarded successfully'
        },
        {
          title: 'TRY Intraday Liquidity',
          timestamp: '2024-03-20 13:30:00',
          status: 'not_started',
          type: 'info',
          description: 'Scheduled for end of day processing'
        }
      ],
      mapBackgroundStyle: {
        backgroundImage: 'url(' + require('@/assets/world-map-empty.png') + ')',
        backgroundSize: 'cover',
        backgroundPosition: 'center'
      }
    }
  },
  computed: {
    mapData() {
      return this.timelineData.map(item => {
        // Convert status to numeric value
        let statusValue;
        switch (item.status) {
          case 'successful':
            statusValue = 95; // Highest value for successful status
            break;
          case 'failed':
            statusValue = 5;  // Low value for failed status
            break;
          case 'not_started':
            statusValue = 50;  // Middle value for not started status
            break;
          default:
            statusValue = 0;   // Default value for unknown status
        }

        return [
          Math.random() * 240 - 120, // Random longitude between -120 and 120
          Math.random() * 80 - 10,   // Random latitude between -10 and 70
          statusValue,               // Value based on status instead of random
          {
            title: item.title,
            status: item.status
          }
        ]
      })
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
      this.$echarts.registerMap('world', worldGeoJson)

      this.chart = this.$echarts.init(this.$refs.chartContainer)

      const option = {
        backgroundColor: 'transparent',
        tooltip: {
          trigger: 'item',
          formatter: ({ name, value }) => {
            if (Array.isArray(value)) {
              return `${value[3].title}<br/>Status: ${value[3].status}<br/>Value: ${value[2]}`
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
            let color;
            if (coordinatorValue === 95) {
              color = '#00FF00';  // Green
            } else if (coordinatorValue === 50) {
              color = '#808080';  // Grey
            } else if (coordinatorValue === 5) {
              color = '#FF0000';  // Red
            } else {
              // Fallback color for any other values
              color = '#808080';  // Default to grey
            }

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