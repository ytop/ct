Requirement

1.1
write a mockup single page ui with 2 tab, 'My Task' and 'Near-Miss Event'.

1.2
'My Task' will display 4 buttons 'Near-Miss Event', 'Regulatory Issue', 'Business-Identified Issue' and 'Audit Issue', each button has badge to display the number of tasks. click any button will jump to 2nd tab.

Under the 4 buttons, there are 2 piechart , one is 'Regulatory issue chart', another is ' Business-Identified issue chart', each chart will display the number of each status among 'Open', 'Pending review', 'Pending approval', 'Pending action', 'Pending audit', 'Closed'.

1.3
'Near-Miss Event' will has 3 sub-division from top to bottom.

1.3.1
1st division is a form to act as a filter area, which has select box  'Issue Type', 'issue Status', 'Created Year', 'L1 Risk Type', 'L2 Risk Type', 'Key Field' , input box 'Key Word', and 4 buttons 'Search', 'Reset', 'Export','New'.

1.3.2
2nd division will display the table of issue list by the filter above, in the fixed right column of each row there are 2 buttons 'Detail' and 'Edit'. the table has column layout, 
<resultMap type="com.web.mra.entity.RegulatoryIssue" id="RegulatoryIssueResult">
    <id property="issueAutoId" column="issue_auto_id"/>
    <result property="linkId" column="link_id"/>
    <result property="issueNo" column="issue_no"/>
    <result property="issueType" column="issue_type"/>
    <result property="uniqueId" column="unique_id"/>
    <result property="issueName" column="issue_name"/>
    <result property="issueDescription" column="issue_description"/>
    <result property="issuedBy" column="issued_by"/>
    <result property="issuanceDate" column="issuance_date"/>
    <result property="formalResponseDate" column="formal_response_date"/>
    <result property="riskRating" column="risk_rating"/>
    <result property="sourceOfIssuance" column="source_of_issuance"/>
    <result property="primaryImpactedRiskArea" column="primary_impacted_risk_area"/>
    <result property="secondaryImpactedRiskArea" column="secondary_impacted_risk_area"/>
    <result property="rootCauseCategory" column="root_cause_category"/>
    <result property="rootCauseSubcategory" column="root_cause_subcategory"/>
    <result property="rootCauseAnalysis" column="root_cause_analysis"/>
    <result property="impactAnalysis" column="impact_analysis"/>
    <result property="recommendation" column="recommendation"/>
    <result property="violationOfLaw" column="violation_of_law"/>
    <result property="violationOfLawComment" column="violation_of_law_comment"/>
    <result property="status" column="status"/>
    <result property="primaryOwnerDept" column="primary_owner_dept"/>
    <result property="secondaryOwnerDepts" column="secondary_owner_depts"/>
    <result property="supportDepts" column="support_depts"/>
    <result property="targetCompletionDate" column="target_completion_date"/>
    <result property="dueType" column="due_type"/>
    <result property="sourceOfClosure" column="source_of_closure"/>
    <result property="closureDate" column="closure_date"/>
</resultMap>

1.3.3
3rd division is a dymamic one, if the buttons 'New', 'Edit' or 'Detail' is clicked, the 2nd divison will be folded. 
3rd divison will show fields of the table above for adding new record, editing old record, or just display the detail info.
3rd divison also has submit button if it is 'new' or 'edit'.


2
develop environment
It is a playground to try various vuejs 2 and element ui.
the package is defined as package.json.

3
Generate the sample data automatically.

4 
Add test case automatically



// Project Structure:
/*
building-monitor/
├── src/
│   ├── assets/
│   │   └── logo.png
│   ├── components/
│   │   ├── FloorDisplay.vue
│   │   └── LocationMarker.vue
│   ├── views/
│   │   └── BuildingView.vue
│   ├── store/
│   │   └── index.js
│   ├── router/
│   │   └── index.js
│   ├── data/
│   │   └── floorData.js
│   ├── App.vue
│   └── main.js
├── package.json
└── vue.config.js
*/

// package.json
{
  "name": "building-monitor",
  "version": "1.0.0",
  "private": true,
  "scripts": {
    "serve": "vue-cli-service serve",
    "build": "vue-cli-service build",
    "lint": "vue-cli-service lint"
  },
  "dependencies": {
    "vue": "^2.6.14",
    "vuex": "^3.6.2",
    "vue-router": "^3.5.3",
    "element-ui": "^2.15.13",
    "echarts": "^5.4.3",
    "core-js": "^3.30.1"
  },
  "devDependencies": {
    "@vue/cli-plugin-babel": "~4.5.0",
    "@vue/cli-plugin-eslint": "~4.5.0",
    "@vue/cli-service": "~4.5.0",
    "babel-eslint": "^10.1.0",
    "eslint": "^6.7.2",
    "eslint-plugin-vue": "^6.2.2",
    "vue-template-compiler": "^2.6.14"
  }
}

// src/main.js
import Vue from 'vue'
import App from './App.vue'
import router from './router'
import store from './store'
import ElementUI from 'element-ui'
import 'element-ui/lib/theme-chalk/index.css'
import * as echarts from 'echarts'

Vue.use(ElementUI)
Vue.prototype.$echarts = echarts

Vue.config.productionTip = false

new Vue({
  router,
  store,
  render: h => h(App)
}).$mount('#app')

// src/App.vue
<template>
  <div id="app">
    <el-container>
      <el-header>
        <h1>Building Monitor</h1>
      </el-header>
      <el-main>
        <router-view></router-view>
      </el-main>
    </el-container>
  </div>
</template>

<script>
export default {
  name: 'App'
}
</script>

<style>
#app {
  font-family: Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}

.el-header {
  background-color: #409EFF;
  color: white;
  line-height: 60px;
}

.el-header h1 {
  margin: 0;
}
</style>

// src/router/index.js
import Vue from 'vue'
import VueRouter from 'vue-router'
import BuildingView from '../views/BuildingView.vue'

Vue.use(VueRouter)

const routes = [
  {
    path: '/',
    name: 'Building',
    component: BuildingView
  }
]

const router = new VueRouter({
  mode: 'history',
  base: process.env.BASE_URL,
  routes
})

export default router

// src/store/index.js
import Vue from 'vue'
import Vuex from 'vuex'

Vue.use(Vuex)

export default new Vuex.Store({
  state: {
    floors: []
  },
  mutations: {
    SET_FLOORS(state, floors) {
      state.floors = floors
    }
  },
  actions: {
    loadFloors({ commit }) {
      // In a real application, this would be an API call
      const floors = require('../data/floorData').default
      commit('SET_FLOORS', floors)
    }
  },
  getters: {
    getFloors: state => state.floors
  }
})

// src/data/floorData.js
export default [
  {
    id: 1,
    name: 'Ground Floor',
    locations: [
      { id: 'loc1', name: 'Reception', x: 100, y: 100, status: 'green' },
      { id: 'loc2', name: 'Meeting Room 1', x: 300, y: 150, status: 'red' },
      { id: 'loc3', name: 'Cafeteria', x: 200, y: 250, status: 'green' }
    ],
    layout: {
      walls: 'M0,0 L400,0 L400,300 L0,300 Z',
      rooms: [
        'M50,50 L150,50 L150,150 L50,150 Z',
        'M200,100 L350,100 L350,200 L200,200 Z'
      ]
    }
  },
  {
    id: 2,
    name: 'First Floor',
    locations: [
      { id: 'loc4', name: 'Office 201', x: 150, y: 100, status: 'green' },
      { id: 'loc5', name: 'Office 202', x: 250, y: 150, status: 'red' }
    ],
    layout: {
      walls: 'M0,0 L400,0 L400,300 L0,300 Z',
      rooms: [
        'M100,50 L200,50 L200,150 L100,150 Z',
        'M250,100 L350,100 L350,200 L250,200 Z'
      ]
    }
  }
]

// src/views/BuildingView.vue
<template>
  <div class="building-view">
    <el-row :gutter="20">
      <el-col :span="24">
        <el-card v-for="floor in floors" 
                :key="floor.id" 
                class="floor-card"
                shadow="hover">
          <div slot="header" class="clearfix">
            <span>{{ floor.name }}</span>
            <el-button style="float: right; padding: 3px 0" type="text"
                      @click="refreshFloor(floor.id)">
              Refresh
            </el-button>
          </div>
          <floor-display :floor="floor"></floor-display>
        </el-card>
      </el-col>
    </el-row>
  </div>
</template>

<script>
import { mapGetters, mapActions } from 'vuex'
import FloorDisplay from '../components/FloorDisplay.vue'

export default {
  name: 'BuildingView',
  components: {
    FloorDisplay
  },
  computed: {
    ...mapGetters(['getFloors']),
    floors() {
      return this.getFloors
    }
  },
  methods: {
    ...mapActions(['loadFloors']),
    refreshFloor(floorId) {
      // Implement refresh logic here
      this.$message({
        message: `Refreshing floor ${floorId}`,
        type: 'success'
      })
    }
  },
  created() {
    this.loadFloors()
  }
}
</script>

<style scoped>
.building-view {
  padding: 20px;
}
.floor-card {
  margin-bottom: 20px;
}
</style>

// src/components/FloorDisplay.vue
<template>
  <div class="floor-display">
    <div ref="chart" class="chart-container"></div>
  </div>
</template>

<script>
export default {
  name: 'FloorDisplay',
  props: {
    floor: {
      type: Object,
      required: true
    }
  },
  data() {
    return {
      chart: null
    }
  },
  methods: {
    initChart() {
      this.chart = this.$echarts.init(this.$refs.chart)
      this.updateChart()
    },
    updateChart() {
      const option = {
        tooltip: {
          formatter: (params) => {
            const location = this.floor.locations.find(loc => loc.id === params.data.id)
            return `${location.name}<br/>Status: ${location.status}`
          }
        },
        series: [
          {
            type: 'custom',
            renderItem: (params, api) => {
              return {
                type: 'group',
                children: [
                  // Draw floor layout
                  {
                    type: 'path',
                    shape: {
                      pathData: this.floor.layout.walls
                    },
                    style: {
                      fill: 'none',
                      stroke: '#333'
                    }
                  },
                  // Draw rooms
                  ...this.floor.layout.rooms.map(room => ({
                    type: 'path',
                    shape: {
                      pathData: room
                    },
                    style: {
                      fill: '#f0f0f0',
                      stroke: '#666'
                    }
                  }))
                ]
              }
            },
            data: [{}]
          },
          {
            type: 'scatter',
            data: this.floor.locations.map(loc => ({
              value: [loc.x, loc.y],
              id: loc.id,
              itemStyle: {
                color: loc.status === 'green' ? '#67C23A' : '#F56C6C'
              }
            })),
            symbol: 'pin',
            symbolSize: 30,
            label: {
              show: true,
              formatter: (params) => {
                const location = this.floor.locations.find(loc => loc.id === params.data.id)
                return location.name
              },
              position: 'top'
            }
          }
        ]
      }

      this.chart.setOption(option)
    }
  },
  mounted() {
    this.initChart()
    window.addEventListener('resize', () => {
      this.chart?.resize()
    })
  },
  beforeDestroy() {
    window.removeEventListener('resize', () => {
      this.chart?.resize()
    })
    this.chart?.dispose()
  },
  watch: {
    floor: {
      deep: true,
      handler() {
        this.$nextTick(() => {
          this.updateChart()
        })
      }
    }
  }
}
</script>

<style scoped>
.floor-display {
  width: 100%;
  height: 100%;
}
.chart-container {
  width: 100%;
  height: 400px;
}
</style>

// src/components/LocationMarker.vue
<template>
  <div class="location-marker" :class="status">
    <el-tooltip :content="tooltipContent" placement="top">
      <div class="marker-icon">
        <i :class="iconClass"></i>
      </div>
    </el-tooltip>
  </div>
</template>

<script>
export default {
  name: 'LocationMarker',
  props: {
    location: {
      type: Object,
      required: true
    }
  },
  computed: {
    status() {
      return this.location.status
    },
    iconClass() {
      return {
        'el-icon-success': this.status === 'green',
        'el-icon-error': this.status === 'red'
      }
    },
    tooltipContent() {
      return `${this.location.name}\nStatus: ${this.status}`
    }
  }
}
</script>

<style scoped>
.location-marker {
  display: inline-block;
  cursor: pointer;
}
.marker-icon {
  font-size: 24px;
}
.green .marker-icon {
  color: #67C23A;
}
.red .marker-icon {
  color: #F56C6C;
}
</style>

// vue.config.js
module.exports = {
  publicPath: process.env.NODE_ENV === 'production' ? '/building-monitor/' : '/',
  devServer: {
    port: 8080,
    open: true
  }
}