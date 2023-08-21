<template>
  <div style="height: 100vh; width: 100vw" id="map" ref="container"></div>
</template>

<script setup lang="ts">
import mapboxgl from 'mapbox-gl';
import type { Map } from 'mapbox-gl';
import type {Ref} from 'vue';
import {GlowwormMap} from 'mapbox-glowworm';
import level1Data from './assets/json/a.json';

const map = shallowRef<Map>();
const container = ref() as Ref<HTMLElement>;

const outGlowwormColorList = [
  'blue',
  '#0060E9',
  'lightBlue',
  '#006AB0',
  'level1',
  '#00856E',
  'orange',
  '#A77D00',
  'yellow',
  '#A97001',
  'deepRed',
  '#931721',
  'red',
  '#F84D04',
];
const innerGlowwormColorList = [
  'blue',
  '#14E6FF',
  'lightBlue',
  '#28A4FF',
  'level1',
  '#00CE5A',
  'orange',
  '#F6A109',
  'yellow',
  '#FFD600',
  'deepRed',
  '#E00F20',
  'red',
  '#E64500',
];

const initMap = () => {
  console.log('container:', container)
  mapboxgl.accessToken = '';
  map.value = new mapboxgl.Map({
    container: container.value,
// Choose from Mapbox's core styles, or make your own style with Mapbox Studio
    style: 'mapbox://styles/mapbox/dark-v11',
    center: [-103.5917, 40.6699],
    zoom: 3
  });
  map.value!.on('load', () => {
    addLayer();
  })
}
const addLayer = () => {
  const glowworm = new GlowwormMap(map.value!);
  console.log('level1Data:', level1Data)
  glowworm.addGlowwormLayer(level1Data, {
    glowwormLayerName: 'glowwormLayerName',
    glowwormInnerColorList: innerGlowwormColorList,
    glowwormOutColorList: outGlowwormColorList,
  })
}
onMounted(() => {
  initMap();
})
</script>

<style scoped lang="scss">

</style>
