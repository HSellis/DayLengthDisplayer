<template>
  <div id="app">
    <h1>Day length calculator</h1>
    <section id="main-container">
      <div>
        <label>Select coordinates from the map:</label>
        <div id="map-area">
          <Map :input-center="mapCoords" :map-clicked="mapClicked"></Map>
        </div>
      </div>
      <div>
        <label>Or enter the coordinates manually:</label>
        <div>
          <label>Latitude: </label>
          <input type="number" min="-90" max="90" step="0.0001" v-model="latitude">
        </div>
        <div>
          <label>Longitude: </label>
          <input type="number" min="-180" max="180" step="0.0001" v-model="longitude">
        </div>
      </div>
      <div id="calculator-area">
        <Calculator :selected-coords="mapCoords"></Calculator>
      </div>
    </section>
  </div>
</template>

<script>
import Map from "./components/Map";
import {latLng} from 'leaflet';
import Calculator from "./components/Calculator";

export default {
  name: 'App',
  data: () => {
    return {
      latitude: 0,
      longitude: 0,
    }
  },
  computed: {
    mapCoords: function () {
      return latLng(parseFloat(this.latitude), parseFloat(this.longitude))
    }
  },
  methods: {
    mapClicked: function (event) {
      this.latitude = Math.round(event.latlng.lat * 10000) / 10000;
      this.longitude = Math.round(event.latlng.lng * 10000) / 10000;
    }
  },
  components: {
    Calculator,
    Map,
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
#main-container {
  width: 100%;
  //display: flex;
  //justify-content: center;
  align-items: center;
  padding: 50px;
}

#main-container div {
  padding: 20px;
}

#map-area {
  width: 75%;
  height: 500px;
  margin: 40px;
  align-items: center;
}



</style>
