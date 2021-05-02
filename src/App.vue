<template>
  <div id="app">
    <h1>Päeva pikkuse kalkulaator</h1>
    <section id="main-container">
      <div id="coordinate-area">
        <div id="info-area">
          <h3>Määra koordinaadid</h3>
          <label>Vali koordinaadid kaardilt</label>
          <br>
          <br>
          <label>Või sisesta koordinaadid käsitsi.</label>
          <div>
            <label>Laiuskraad: </label>
            <input type="number" min="-90" max="90" step="0.0001" v-model="latitude">
          </div>
          <div>
            <label>Pikkuskraad: </label>
            <input type="number" min="-180" max="180" step="0.0001" v-model="longitude">
          </div>
        </div>
        <div id="map-area">
          <Map :input-center="mapCoords" :map-clicked="mapClicked"></Map>
        </div>
      </div>
      <div id="calculator">
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
  margin: 10px;
  height: 125vh;
  width: auto;
}
#main-container {
  width: 100%;
  height: 100%;
  align-items: center;
}

#coordinate-area {
  width: 100%;
  height: 40%;
  display: flex;
  justify-content: left;
}

#calculator {
  width: 100%;
  height: 60%;
}

#coordinate-area > div {
  border: solid 2px black;
}

#info-area {
  width: 25%;
}

#map-area {
  width: 75%;
}

</style>
