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
      <l-marker :lat-lng="inputCenter"></l-marker>
    </l-map>
  </section>
</template>

<script>
import {LMap, LTileLayer, LMarker} from "vue2-leaflet";
import {latLng} from 'leaflet'
import 'leaflet/dist/leaflet.css'

// for fixing a bug with LMarker
import L from 'leaflet';
delete L.Icon.Default.prototype._getIconUrl;

export default {
  name: "Map",
  components: {
    LMap,
    LTileLayer,
    LMarker
  },
  data: () => {
    return {
      center: [0, 0],
      zoom: 2,
      url: 'https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png',
      attribution: '&copy; <a href="http://osm.org/copyright">OpenStreetMap</a> contributors',
      mapOptions: {
        zoomSnap: 0.5
      },
    }
  },
  methods: {
    zoomUpdate(zoom) {
      this.zoom = zoom;
    },
    centerUpdate(center) {
      this.center = center;
    }
  },
  props: {
    mapClicked: Function,
    inputCenter: Object,
  },
  watch: {
    inputCenter: function (value) {
      this.center = latLng(value.lat, value.lng)
    }
  },
  mounted() {
    // for fixing a bug with LMarker, code from https://github.com/Leaflet/Leaflet/issues/4968#issuecomment-483402699
    L.Icon.Default.mergeOptions({
      iconRetinaUrl: require('leaflet/dist/images/marker-icon-2x.png'),
      iconUrl: require('leaflet/dist/images/marker-icon.png'),
      shadowUrl: require('leaflet/dist/images/marker-shadow.png'),
    });
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