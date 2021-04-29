<template>
  <div id="app">
    <h1>Day length calculator</h1>
    <section id="main-container">
      <div>
        <label>Set the date:</label>
        <input type="datetime-local" v-model="dateString">
      </div>

      <div>
        <label>and select coordinates from the map:</label>
        <div id="map-area">
          <Map :map-clicked="mapClicked"></Map>
        </div>
      </div>

      <div>
        <label>Or enter the coordinates manually:</label>
        <input type="number" placeholder="Latitude" v-model="latitude">
        <input type="number" placeholder="Longitude" v-model="longitude">
      </div>

      <div>
        <button @click="calculateTimeInfo">Calculate</button>
      </div>

      <div id="results-container">
        <label>Päikesetõus: {{sunRise}}</label>
        <label>Päikeseloojang: {{sunSet}}</label>
        <label>Päeva pikkus: {{dayLength}}</label>
      </div>
    </section>


  </div>
</template>

<script>
import Map from "./components/Map";

const SunCalc = require('suncalc');

export default {
  name: 'App',
  data: () => {
    return {
      dateString: null,
      latitude: null,
      longitude: null,

      sunRise: null,
      sunSet: null,
      dayLength: null,
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

      let times = SunCalc.getTimes(new Date(this.dateString), this.latitude, this.longitude);
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
      this.latitude = event.latlng.lat;
      this.longitude = event.latlng.lng;
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
