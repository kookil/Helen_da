<template>
  <div class="parallax-header">
    <div class="parallax-bg"></div>
    <h1 class="title">Hurricane Helene and Appalachia in NC</h1>
  </div>
</template>

<style scoped>
.parallax-header {
  position: relative;
  height: 100vh;
  overflow: hidden;
}

.parallax-bg {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-image: url('@/assets/img/Helene2024_geocolor.gif');
  background-size: cover;
  background-attachment: fixed;
  background-position: center;

  /* 添加模糊渐变效果 */
  mask-image: linear-gradient(to bottom, black 40%, transparent 100%);
  -webkit-mask-image: linear-gradient(to bottom, black 40%, transparent 100%);
}

.title {
  position: relative;
  text-align: center;
  color: white;
  font-size: 5rem;
  font-family: 'Anton', sans-serif;
  margin-top: 25%;
  text-shadow: 2px 4px 12px rgba(0, 0, 0, 0.7);
}
</style>
