<template>
  <div id="app">
    <h1>Day length calculator</h1>
    <section id="main-container">
      <div>
        <label>Select coordinates from the map:</label>
        <div id="map-area">
          <Map :input-center="mapCenter" :map-clicked="mapClicked"></Map>
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

      <div>
        <label>Set the date:</label>
        <input type="datetime-local" v-model="dateString">
      </div>

      <div>
        <button @click="calculateTimeInfo">Calculate</button>
      </div>

      <div id="results-container">
        <label>Sunrise: {{sunRise}}</label>
        <br>
        <label>Sunset: {{sunSet}}</label>
        <br>
        <label>Length of day: {{dayLength}}</label>
        <br>
      </div>
    </section>


  </div>
</template>

<script>
import Map from "./components/Map";
import {latLng} from 'leaflet';
const SunCalc = require('suncalc');

export default {
  name: 'App',
  data: () => {
    return {
      dateString: null,
      latitude: 0,
      longitude: 0,

      sunRise: null,
      sunSet: null,
      dayLength: null,
    }
  },
  computed: {
    mapCenter: function () {
      return latLng(parseFloat(this.latitude), parseFloat(this.longitude))
    }
  },
  methods: {
    calculateTimeInfo: function () {
      if (!this.dateString) {
        alert("Please select a date")
        return
      }
      if (!this.longitude) {
        alert("Please specify a longitude")
        return;
      }
      if (!this.latitude) {
        alert("PLease specify a latitude")
        return;
      }

      let times = SunCalc.getTimes(new Date(this.dateString), parseFloat(this.latitude), parseFloat(this.longitude));
      let sunriseStr = times.sunrise.getHours() + ':' + times.sunrise.getMinutes();
      let sunsetStr = times.sunset.getHours() + ':' + times.sunset.getMinutes();
      let dayLengthMinutes = (times.sunset - times.sunrise) / 1000 / 60;
      let dayLengthHours = Math.floor(dayLengthMinutes / 60);
      let dayLengthTime = dayLengthHours + 'h ' + Math.round(dayLengthMinutes - dayLengthHours * 60) + 'min';

      this.sunRise = sunriseStr
      this.sunSet = sunsetStr
      this.dayLength = dayLengthTime
    },
    mapClicked: function (event) {
      this.latitude = Math.round(event.latlng.lat * 10000) / 10000;
      this.longitude = Math.round(event.latlng.lng * 10000) / 10000;
    }
  },
  components: {
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
