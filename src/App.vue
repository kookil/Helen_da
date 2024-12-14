<template>
  <div id="app">
    <!-- title -->
    <ParallaxHeader />

    <!-- Map -->
    <MapSection />

    <!-- Data -->
    <TableSection />

    <!-- GIS-->
    <GISAnalysisSection />
  </div>
</template>

<script>
import ParallaxHeader from './components/ParallaxHeader.vue'
import MapSection from './components/MapSection.vue'
import TableSection from './components/TableSection.vue'
import GISAnalysisSection from './components/GISAnalysisSection.vue'

export default {
  components: {
    ParallaxHeader,
    MapSection,
    TableSection,
    GISAnalysisSection,
  },
}
</script>

<style>
body {
  margin: 0;
  font-family: Arial, sans-serif;
}
</style>

