<template>
  <section>
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
</template>

<script>
const SunCalc = require('suncalc');

export default {
  name: "Calculator",
  data: () => {
    return {
      dateString: null,

      sunRise: null,
      sunSet: null,
      dayLength: null,
    }
  },
  props: {
    selectedCoords: Object,
  },
  methods: {
    calculateTimeInfo: function () {
      if (!this.dateString) {
        alert("Please select a date")
        return
      }
      if (!this.selectedCoords) {
        alert("Please specify coordinates")
        return;
      }

      let times = SunCalc.getTimes(new Date(this.dateString), this.selectedCoords.lat, this.selectedCoords.lng);
      let sunriseStr = times.sunrise.getHours() + ':' + times.sunrise.getMinutes();
      let sunsetStr = times.sunset.getHours() + ':' + times.sunset.getMinutes();
      let dayLengthMinutes = (times.sunset - times.sunrise) / 1000 / 60;
      let dayLengthHours = Math.floor(dayLengthMinutes / 60);
      let dayLengthTime = dayLengthHours + 'h ' + Math.round(dayLengthMinutes - dayLengthHours * 60) + 'min';

      this.sunRise = sunriseStr
      this.sunSet = sunsetStr
      this.dayLength = dayLengthTime
    },
  }
}
</script>

<style scoped>

</style>