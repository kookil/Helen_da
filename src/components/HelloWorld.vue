<template>
  <div id="app">
    <!-- 视差滚动的标题 -->
    <ParallaxHeader />

    <!-- 地图组件 -->
    <MapSection />

    <!-- 数据可视化表格 -->
    <TableSection />

    <!-- GIS 分析功能 -->
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
  font-family: Anton, sans-serif;
}
</style>
