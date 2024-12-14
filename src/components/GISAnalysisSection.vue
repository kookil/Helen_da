<template>
  <div class="container">
    <div class="image-container">
      <img src="@/assets/img/risk.png" alt="High Rain Map" class="map-image" />
    </div>
    <div class="description-container">
      <h2>Flood Risk Analysis</h2>
      <p>
        Hurricane Helene brought heavy rainfall and flooding to many areas, with the impacts
        particularly severe in the Appalachian region. One of the hardest-hit areas was Asheville,
        North Carolina, where the storm's heavy rainfall resulted in widespread flooding and severe
        damage. This event highlighted the importance of understanding which areas are most
        vulnerable to this type of flooding.
      </p>
      <p>
        To analyze flood-prone areas, topographic and precipitation data were utilized. Low slope
        areas (defined as areas with a slope less than 3°) were identified using DEM data from the
        SRTM dataset. Additionally, high rainfall areas with rainfall exceeding 100 mm were
        identified using GPM precipitation data. Combining these factors gives a simple idea of
        flood risk areas, and it can be seen that Asheville is indeed among them.
      </p>
    </div>
  </div>
</template>


<script>
export default {
  name: "FloodRiskAnalysis",
};
</script>

<style scoped>
.container {
  display: flex;
  flex-direction: row;
  height: 100vh;
}

.image-container {
  flex: 1.5;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  padding: 10px;
  gap: 20px;
}

.map-image {
  width: 90%;
  border: 1px solid #ccc;
  box-shadow: 0px 4px 6px rgba(0, 0, 0, 0.1);
}

.description-container {
  flex: 1;
  padding: 20px;
  background-color: #f9f9f9;
  overflow-y: auto;
  margin-top: 200px;
  margin-bottom: 200px;
}


.description-container h2 {
  font-family: 'Arial', sans-serif;
  font-size: 24px;
  margin-bottom: 10px;
}

.description-container p {
  font-family: 'Arial', sans-serif;
  font-size: 16px;
  line-height: 1.5;
  margin-bottom: 10px;
}

.description-container ul {
  margin-left: 20px;
  list-style: disc;
}

/* Add styling to center GISAnalysisSection if needed */
.gis-analysis {
  display: flex;
  justify-content: center;
  align-items: center;
  text-align: center;
  height: 100%;
}
</style>
