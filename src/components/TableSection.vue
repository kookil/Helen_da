<template>
  <div id="html-container">
    <!-- Heatmap iframe -->
    <iframe 
      src="/heatmap.html" 
      frameborder="0" 
      width="100%" 
      height="500px">
    </iframe>

    <!-- Parameter description -->
    <div id="parameter-info">
      Parameter(s): 
      <br>TS - MERRA-2 Earth Skin Temperature (°C)
      <br>RH2M - MERRA-2 Relative Humidity at 2 Meters (%)
      <br>WS2M - MERRA-2 Wind Speed at 2 Meters (m/s)
    </div>
  </div>
</template>

<script>
export default {
  name: 'TableSection',
};
</script>

<style scoped>
#html-container {
  position: relative;
  height: 100%;
}

iframe {
  border: none;
}

#parameter-info {
  position: absolute;
  bottom: -30px;
  left: 10px;
  background-color: rgba(0, 0, 0, 0.6); /* 半透明背景 */
  color: white;
  font-family: 'Anton', sans-serif;
  font-size: 14px;
  padding: 10px;
  border-radius: 5px;
  max-width: 350px; /* 控制文字区域宽度 */
}
</style>
