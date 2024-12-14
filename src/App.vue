<template>
  <div id="app">
    <world-map></world-map>
  </div>
</template>

<script>
import WorldMap from '@/components/WorldMap.vue'

export default {
  name: 'App',
  components: {
    WorldMap
  }
}
</script>

<style>
html, body {
  margin: 0;
  padding: 0;
  height: 100%;
}

#app {
  height: 100%;
}
</style>
