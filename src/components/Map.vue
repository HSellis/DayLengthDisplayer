<template>
  <section id="map-container">
    <l-map
        :zoom="zoom"
        :center="center"
        :options="mapOptions"
        @update:center="centerUpdate"
        @update:zoom="zoomUpdate"
        @click="mapClicked"
    >
      <l-tile-layer
          :url="url"
          :attribution="attribution"
      />
    </l-map>
  </section>
</template>

<script>
import {LMap, LTileLayer} from "vue2-leaflet";
import {latLng} from 'leaflet'
import 'leaflet/dist/leaflet.css'

export default {
  name: "Map",
  components: {
    LMap,
    LTileLayer
  },
  data: () => {
    return {
      zoom: 10,
      center: latLng(47.4, -1.1),
      url: 'https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png',
      attribution: '&copy; <a href="http://osm.org/copyright">OpenStreetMap</a> contributors',
      currentZoom: 11.5,
      currentCenter: latLng(47.41322, -1.219482),
      mapOptions: {
        zoomSnap: 0.5
      },
    }
  },
  methods: {
    zoomUpdate(zoom) {
      this.currentZoom = zoom;
    },
    centerUpdate(center) {
      this.currentCenter = center;
    }
  },
  props: {
    mapClicked: Function
  }
}
</script>

<style scoped>

#map-container {
  width: 100%;
  height: 100%;
  display: flex;
  justify-content: center;
}

</style>