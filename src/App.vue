<template>
  <div id="app">
    <h1>Day length calculator</h1>
    <section id="main-container">
      <div>
        <label>Set the date:</label>
        <input type="datetime-local">
      </div>

      <div>
        <label>and select coordinates from the map:</label>
        <div id="map-area">
          <Map></Map>
        </div>
      </div>

      <div>
        <label>Or enter the coordinates manually:</label>
        <InfoInput :display-day-length-info="displayDayLengthInfo"></InfoInput>
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
import InfoInput from "./components/InfoInput";
import Map from "./components/Map";

const SunCalc = require('suncalc');

export default {
  name: 'App',
  data: () => {
    return {
      sunRise: 0,
      sunSet: 0,
      dayLength: 0,
      url: 'https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png'
    }
  },
  methods: {
    displayDayLengthInfo: function (latitude, longitude, date) {

      //let dayTimeInfo = this.calculateDayTimeInfo(latitude, longitude, date)
      let times = SunCalc.getTimes(date, latitude, longitude);
      let sunriseStr = times.sunrise.getHours() + ':' + times.sunrise.getMinutes();
      let sunsetStr = times.sunset.getHours() + ':' + times.sunset.getMinutes();
      let dayLengthMinutes = (times.sunset - times.sunrise) / 1000 / 60;
      let dayLengthHours = Math.floor(dayLengthMinutes / 60)
      let dayLengthTime = dayLengthHours + 'h ' + Math.round(dayLengthMinutes - dayLengthHours * 60) + 'min'

      this.sunRise = sunriseStr
      this.sunSet = sunsetStr
      this.dayLength = dayLengthTime
    }
  },
  components: {
    Map,
    InfoInput,
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
