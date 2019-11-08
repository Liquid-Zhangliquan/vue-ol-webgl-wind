<template>
  <div id="olmap"></div>
</template>

<script>
import 'ol/ol.css';
import Map from 'ol/Map.js';
import View from 'ol/View.js';
import TileLayer from 'ol/layer/Tile.js';
import OSM from 'ol/source/OSM';

import UVBuffer from './lib/ol-webgl-wind/UVBuffer';
import { CanvasWindParticlesLayer } from './lib/ol-webgl-wind/CanvasWindParticlesLayer';

export default {
  name: 'ol-wind',
  components: {},
  mounted() {
    this.mapInit();
  },
  methods: {
    mapInit() {
      const layers = [
        new TileLayer({
          source: new OSM()
        })
      ];

      const map = new Map({
        layers: layers,
        target: 'olmap',
        view: new View({
          center: [115.79, 36.142],
          projection: 'EPSG:4326',
          zoom: 8
        })
      });
      fetch('/data/gfs.json').then(response => {
        response.json().then(res => {
          const data = res['2019110814'];
          console.log(data);
          this.windLayerInit(map, data)
        });
      });
    },
    windLayerInit(map, data) {
      const width = data[0].header.nx;
      const height = data[0].header.ny;
      const extent = [data[0].header.lo1, data[0].header.la1, data[0].header.lo2, data[0].header.la2];

      const uBuffer = data[0].data;
      const vBuffer = data[1].data;
      const uvBuffer = new UVBuffer(uBuffer, vBuffer, width, height, extent);

      const INITIAL_TTL = 50;
      const NUMBER_OF_PARTICULES = 10000;
      const PARTICLE_FADING = 0.95;

      const windParticlesLayer = new CanvasWindParticlesLayer({
        map,
        uvBuffer,
        particles: NUMBER_OF_PARTICULES,
        fading: PARTICLE_FADING,
        ttl: INITIAL_TTL
      });
      map.addLayer(windParticlesLayer);
    }
  }
};
</script>

<style>
html,
body,
#app {
  width: 100%;
  height: 100%;
  padding: 0px;
  margin: 0px;
  overflow: hidden;
}
#olmap {
  width: 100%;
  height: 100%;
}
</style>
