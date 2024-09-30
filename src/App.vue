<template>
  <div class="app-main">
    <div style="height: 100vh; width: 100vw" id="map" ref="container"></div>
    <div class="tool-bar">
      <span @click="handleChangeType('萤火虫')" class="tool-bar-item"
        :class="{ 'active': activeType === '萤火虫' }">萤火虫</span>
      <span @click="handleChangeType('光环')" class="tool-bar-item" :class="{ 'active': activeType === '光环' }">光环</span>
    </div>
    <div class="tool-bar tool-bar-change">
      <span @click="updateSource" class="tool-bar-item">切换数据</span>
    </div>
  </div>
</template>

<script setup lang="ts">
import mapboxgl from 'mapbox-gl';
import type { Map } from 'mapbox-gl';
import type { Ref } from 'vue';
import GlowwormMap from 'mapbox-glowworm';
import type { LayerInstance } from 'mapbox-glowworm';
// const GlowwormMap = window.Glowworm;
import westData from './assets/json/a.json';
import newYorkData from './assets/json/coordinates.json';
import { outGlowwormColorList, innerGlowwormColorList } from './assets/js/mapColor';

const map = shallowRef<Map>();
const glowworm = shallowRef<GlowwormMap>();
const glowwromInstance = shallowRef<LayerInstance>();
const commonLayer = shallowRef();
const commonLayerInstance = shallowRef<LayerInstance>();
const container = ref() as Ref<HTMLElement>;
const mapDotDataType = ref<'west'|'newYork'>('west');
const mapDotData = shallowRef(westData);

const COMMON_LAYER_NAME = 'common';
const GLOWWORM_LAYER_NAME = 'glowworm';

type ActiveType = '萤火虫' | '光环';
const activeType = ref<ActiveType>('萤火虫');
const handleChangeType = (type: ActiveType) => {
  if (type === activeType.value) return;
  activeType.value = type;
  if (activeType.value === '光环') {
    if (map.value?.getLayer(COMMON_LAYER_NAME)) {
      updateCommon();
      commonLayerInstance.value?.show();
    } else {
      addCommonLayer();
    }
    glowwromInstance.value?.hide();
  } else {
    if (map.value?.getLayer(GLOWWORM_LAYER_NAME)) {
      updateGlowworm()
      glowwromInstance.value?.show();
    } else {
      addGlowwormLayer();
    }
    commonLayerInstance.value?.hide();
  }
}

const initMap = () => {
  mapboxgl.accessToken = 'pk.eyJ1IjoicXVha2UxMjMiLCJhIjoiY2xkY3h6MjVlMGYzdzNxazZ3aDFpY2QzMiJ9.Nz_YiGrkrmj8SHGO6r72hg';
  map.value = new mapboxgl.Map({
    container: container.value,
    // Choose from Mapbox's core styles, or make your own style with Mapbox Studio
    style: 'mapbox://styles/mapbox/dark-v11',
    center: [-103.5917, 40.6699],
    zoom: 3
  });
  map.value!.on('load', () => {
    glowworm.value = new GlowwormMap(map.value!);
    if (activeType.value === '萤火虫') {
      addGlowwormLayer();
    } else {
      addCommonLayer()
    }
  })
}
const addCommonLayer = () => {
  commonLayerInstance.value = glowworm.value!.addCommonLayer(mapDotData.value, COMMON_LAYER_NAME)
}
const addGlowwormLayer = () => {
  glowwromInstance.value = glowworm.value!.addGlowwormLayer(mapDotData.value, {
    glowwormLayerName: GLOWWORM_LAYER_NAME,
  })
}
const updateSource = () => {
  if(mapDotDataType.value === 'west') {
    mapDotDataType.value = 'newYork';
    mapDotData.value = newYorkData;
  } else {
    mapDotDataType.value = 'west';
    mapDotData.value = westData;
  }
  if (activeType.value === '萤火虫') {
    updateGlowworm();
  } else {
    updateCommon();
  }
}
const updateCommon = () => {
  commonLayerInstance.value?.updateSource(mapDotData.value);
}
const updateGlowworm = () => {
  glowwromInstance.value?.updateSource(mapDotData.value);
}
onMounted(() => {
  initMap();
})
</script>

<style scoped lang="scss">
.app-main {
  .tool-bar {
    position: fixed;
    top: 16px;
    right: 25px;
    color: #fff;
    z-index: 2025;
    background: rgba(0, 171, 122, 0.1);
    border: 1px solid rgba(0, 171, 122, 0.1);
    padding: 6px 12px;

    &.tool-bar-change {
      right: 200px;
      user-select: none;
      .tool-bar-item {
        &:first-child {
          margin-right: 0;
          border-right: none;
        }
      }
    }

    .tool-bar-item {
      cursor: pointer;

      &.active {
        color: #00AB7A
      }

      &:first-child {
        margin-right: 16px;
        border-right: 1px solid rgba(0, 171, 122, 0.1);
      }
    }
  }
}
</style>
