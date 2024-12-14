<template>
  <div class="container">
    <!-- 左侧地图和单选按钮 -->
    <div class="gee">
      <div id="gee-map"></div>
      <div class="gif-controls">
        <label>
          <input type="radio" name="gif" value="gif1" checked>
          RGB Image
        </label>
        <label>
          <input type="radio" name="gif" value="gif2">
          2024 NDVI Image
        </label>
        <label>
          <input type="radio" name="gif" value="gif3">
          2023 NDVI Image
        </label>
        <label>
          <input type="radio" name="gif" value="gif4">
          LandUse Image
        </label>
      </div>
    </div>

    <!-- 右侧 OSM 地图 -->
    <div class="leaflet-map">
      <div id="leaflet-map"></div>
    </div>
  </div>
</template>

<script>
import L from 'leaflet';
import 'leaflet/dist/leaflet.css';
import gif1 from '@/assets/img/s2.gif';
import gif2 from '@/assets/img/ndvi.gif';
import gif3 from '@/assets/img/ndvi2023.gif';
import gif4 from '@/assets/img/landuse.gif';
import countyPop from '@/assets/data/countyPop.json';

export default {
  name: 'MapSection',
  mounted() {
    // 初始化左侧 ArcGIS 地图
    const geeMap = L.map('gee-map').setView([35.5, -82], 8);
    const arcgisLayer = L.tileLayer('https://services.arcgisonline.com/arcgis/rest/services/NatGeo_World_Map/MapServer/tile/{z}/{y}/{x}', {
      attribution: '&copy; <a href="https://www.esri.com/">Esri</a>',
    });
    arcgisLayer.addTo(geeMap);

    // 初始化右侧现代 OSM 地图
    const leafletMap = L.map('leaflet-map').setView([35.5, -82], 8);
    const modernOsmLayer = L.tileLayer('https://{s}.basemaps.cartocdn.com/rastertiles/voyager/{z}/{x}/{y}{r}.png', {
      attribution: '&copy; <a href="https://carto.com/">CartoDB</a>',
      subdomains: 'abcd',
      maxZoom: 19,
    });
    modernOsmLayer.addTo(leafletMap);
    const opacityControl = L.control({ position: 'topright' });
    opacityControl.onAdd = function () {
      const div = L.DomUtil.create('div', 'opacity-control');
      div.innerHTML = `
        <label for="opacity-slider" style="font-size: 14px; font-weight: bold;">Adjust Opacity:</label>
        <input id="opacity-slider" type="range" min="0" max="1" step="0.01" value="0.7">
      `;
      return div;
    };
    opacityControl.addTo(geeMap);

    // 监听滑块事件，调节 GIF 图层透明度
    const opacitySlider = document.getElementById('opacity-slider');
    opacitySlider.addEventListener('input', function (event) {
      const opacity = parseFloat(event.target.value);
      currentGifOverlay.setOpacity(opacity);
    });

    // 将 JSON 数据转换为 GeoJSON 格式
    const geojsonData = {
      type: 'FeatureCollection',
      features: countyPop.features.map(feature => ({
        type: 'Feature',
        properties: feature.attributes,
        geometry: {
          type: 'Polygon',
          coordinates: feature.geometry.rings,
        },
      })),
    };

    // 添加 GeoJSON 图层到右侧地图
    const geojsonLayer = L.geoJson(geojsonData, {
      style: feature => ({
        fillColor: getColor(feature.properties.POPULATION),
        weight: 2,
        opacity: 1,
        color: 'white',
        fillOpacity: 0.7,
      }),
      onEachFeature: (feature, layer) => {
        layer.on({
          mouseover: highlightFeature,
          mouseout: resetHighlight,
          click: zoomToFeature,
        });
        layer.bindPopup(
          `<b>County:</b> ${feature.properties.NAMELSADCO}<br>
          <b>Population:</b> ${feature.properties.POPULATION.toFixed(1)}`
        );
      },
    }).addTo(leafletMap);


    const legendColors = [
  { color: '#419bdf', description: 'Water' },
  { color: '#397d49', description: 'Trees' },
  { color: '#88b053', description: 'Grass' },
  { color: '#7a87c6', description: 'Flooded Vegetation' },
  { color: '#e49635', description: 'Crops' },
  { color: '#dfc35a', description: 'Shrub and Scrub' },
  { color: '#c4281b', description: 'Built' },
  { color: '#a59b8f', description: 'Bare' },
  { color: '#b39fe1', description: 'Snow and Ice' },
];

// 创建图示控件
let gif4Legend = L.control({ position: 'bottomright' });

gif4Legend.onAdd = function () {
  const div = L.DomUtil.create('div', 'info legend');
  div.innerHTML = '<h4>Land Cover Legend</h4>';
  legendColors.forEach(item => {
    div.innerHTML += `
      <i style="background:${item.color}; width: 18px; height: 18px; display: inline-block; margin-right: 8px;"></i>
      ${item.description}<br>`;
  });
  return div;
};


    // 添加 GIF 图层到左侧地图
    const gifBounds = [[34.8338717, -84.3634799], [36.5519070, -79.9896513]];
    let currentGifOverlay = L.imageOverlay(gif1, gifBounds, { opacity: 0.7 });
    currentGifOverlay.addTo(geeMap);

    // 切换 GIF 图层
    const gifControls = document.querySelectorAll('input[name="gif"]');
    gifControls.forEach(control => {
      control.addEventListener('change', function () {
        geeMap.removeLayer(currentGifOverlay); // 移除当前 GIF 图层

        // 根据选择加载新 GIF
        if (this.value === 'gif1') {
          currentGifOverlay = L.imageOverlay(gif1, gifBounds, { opacity: 0.7 });gif4Legend.remove();
        } else if (this.value === 'gif2') {
          currentGifOverlay = L.imageOverlay(gif2, gifBounds, { opacity: 0.7 });gif4Legend.remove();
        } else if (this.value === 'gif3') {
          currentGifOverlay = L.imageOverlay(gif3, gifBounds, { opacity: 0.7 });gif4Legend.remove();
        }else if (this.value === 'gif4') {
          currentGifOverlay = L.imageOverlay(gif4, gifBounds, { opacity: 0.7 });gif4Legend.addTo(geeMap);
        }
        currentGifOverlay.addTo(geeMap); // 添加新 GIF 图层
      });
    });
    // 同步两个地图
    function syncMaps(map1, map2) {
      map1.on('move', () => {
        map2.setView(map1.getCenter(), map1.getZoom(), { animate: false });
      });
      map2.on('move', () => {
        map1.setView(map2.getCenter(), map2.getZoom(), { animate: false });
      });
    }
    syncMaps(geeMap, leafletMap);

    // 定义颜色映射函数
    function getColor(population) {
      return population > 1500 ? '#f0f921' :
             population > 1000 ? '#f58f45' :
             population > 500  ? '#e7705a' :
             population > 200  ? '#d95969' :
             population > 100  ? '#b12b8e' :
             population > 50   ? '#7704a7' :
             population > 10   ? '#15078a' : '#15078a';
    }

    // 高亮特征
    function highlightFeature(e) {
      const layer = e.target;
      layer.setStyle({
        weight: 5,
        color: '#666',
        fillOpacity: 0.9,
      });
    }

    // 取消高亮
    function resetHighlight(e) {
      geojsonLayer.resetStyle(e.target);
    }

    // 缩放到特征
    function zoomToFeature(e) {
      leafletMap.fitBounds(e.target.getBounds());
    }

    // 添加右侧地图的图例
    const legend = L.control({ position: 'bottomright' });
    legend.onAdd = function () {
      const div = L.DomUtil.create('div', 'info legend');
      div.innerHTML += '<h4 style="margin-bottom: 8px; font-weight: bold;">Population</h4>';
      const grades = [0, 10, 50, 100, 200, 500, 1000, 1500];
      for (let i = 0; i < grades.length; i++) {
        div.innerHTML +=
          `<i style="background:${getColor(grades[i] + 1)}; width: 18px; height: 18px; display: inline-block; margin-right: 8px; opacity: 0.7;"></i> 
           ${grades[i]}${grades[i + 1] ? '&ndash;' + grades[i + 1] + '<br>' : '+'}`;
      }
      return div;
    };
    legend.addTo(leafletMap);
  },
};
</script>


<style scoped>
/* 布局样式 */
.container {
  display: flex;
  justify-content: space-around; /* 平分左右间距 */
  align-items: center; /* 垂直居中 */
  height: 100vh;
}

.gee,
.leaflet-map {
  flex: 0 0 45%; /* 每个地图占 40% 的宽度 */
  height: 70%; /* 高度占 70% 的视口 */
  margin: 0 10px;
  border: 1px solid #ccc; /* 可选：为地图加边框 */
}

#gee-map,
#leaflet-map {
  height: 100%;
  width: 100%;
}
/* 图例样式 */
.info.legend {
  background: white; /* 图例背景色 */
  padding: 10px; /* 图例内边距 */
  line-height: 1.5em; /* 每行高度 */
  border-radius: 5px; /* 圆角效果 */
  box-shadow: 0 0 15px rgba(0, 0, 0, 0.2); /* 阴影效果 */
  font-size: 12px; /* 字体大小 */
}

.info.legend h4 {
  font-size: 14px; /* 标题字体大小 */
  font-weight: bold; /* 标题加粗 */
  margin-bottom: 8px; /* 标题与内容的间距 */
  text-align: center; /* 标题居中 */
}

.info.legend i {
  display: inline-block; /* 确保颜色块为内联块 */
  width: 18px; /* 颜色块宽度 */
  height: 18px; /* 颜色块高度 */
  margin-right: 8px; /* 颜色块与文字的间距 */
  opacity: 0.7; /* 设置透明度 */
}


/* 滑块控件样式 */
.opacity-control {
  background: white;
  padding: 10px;
  border-radius: 5px;
  box-shadow: 0 0 15px rgba(0, 0, 0, 0.2);
}

.opacity-control label {
  display: block;
  margin-bottom: 5px;
  font-weight: bold;
}

.opacity-control input[type='range'] {
  width: 100%;
}

.radio-container label {
  font-family: 'Anton', sans-serif; /* 只对 .radio-container 下的 label 生效 */
}
/* 响应式布局支持（可选） */
@media (max-width: 768px) {
  .container {
    flex-direction: column; /* 屏幕较小时将地图堆叠显示 */
  }

  .gee,
  .leaflet-map {
    height: 50%; /* 每个地图占屏幕一半高度 */
    border-right: none; /* 移除分隔线 */
  }
}
</style>

