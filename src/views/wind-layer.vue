<template>
  <div id="olmap"></div>
</template>

<script>
import 'ol/ol.css';
import Map from 'ol/Map.js';
import View from 'ol/View.js';
import TileLayer from 'ol/layer/Tile.js';
import XYZ from 'ol/source/XYZ'
import Projection from 'ol/proj/Projection';
// import OlWind from 'wind-layer/dist/OlWindy';
import OlWind from '../lib/wind-layer';

export default {
  name: 'wind-layer',
  components: {},
  mounted() {
    this.mapInit();
  },
  methods: {
    mapInit() {
      const layers = new TileLayer({
        title: '午夜蓝图',
        source: new XYZ({
          url: 'http://map.geoq.cn/ArcGIS/rest/services/ChinaOnlineStreetPurplishBlue/MapServer/tile/{z}/{y}/{x}'
        })
      });

      const map = new Map({
        layers: [layers],
        target: 'olmap',
        view: new View({
          center: [108.62957031250004, 36.510877929687524],
          projection: 'EPSG:4326',
          zoom: 5
        })
      });
      map.on('click', (evt) => {
        console.log(evt)
      })
      fetch('/data/gfs.json').then(response => {
        response.json().then(res => {
          const data = res['2019110814'];
          console.log(data);
          this.windLayerInit(map, data);
        });
      });
    },

    windLayerInit(map, data) {
      const wind = new OlWind(data, {
        layerName: 'data',
        projection: 'EPSG:4326',
        // projection: new Projection({ code: 'EPSG:4326',units:'degrees',worldExtent:[-180, -90, 180, 90],metersPerUnit:111319.49079327358 }),
        ratio: 1,
        map: map,
        colorScale: [
          // 'rgb(36,104, 180)',
          // 'rgb(60,157, 194)',
          // 'rgb(128,205,193 )',
          // 'rgb(151,218,168 )',
          // 'rgb(198,231,181)',
          // 'rgb(238,247,217)',
          // 'rgb(255,238,159)',
          'rgb(252,217,125)',
          'rgb(255,182,100)',
          'rgb(252,150,75)',
          'rgb(250,112,52)',
          'rgb(245,64,32)',
          'rgb(237,45,28)',
          'rgb(220,24,32)',
          'rgb(180,0,35)'
        ],
        minVelocity: 0,
        maxVelocity: 10,
        velocityScale: 0.005,
        particleAge: 90,
        lineWidth: 1,
        particleMultiplier: 0.008
      });

      wind.appendTo(map);
    }
  }
};
</script>

<style>
#olmap {
  width: 100%;
  height: 100%;
}
</style>
