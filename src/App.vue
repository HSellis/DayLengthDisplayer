<template>
  <div id="app">
    <InfoInput :display-day-length-info="displayDayLengthInfo"></InfoInput>
    <div>
      <label>Päikesetõus: {{sunRise}}</label>
      <label>Päikeseloojang: {{sunSet}}</label>
      <label>Päeva pikkus: {{dayLength}}</label>
    </div>

  </div>
</template>

<script>
import InfoInput from "./components/InfoInput";

export default {
  name: 'App',
  data: () => {
    return {
      sunRise: 0,
      sunSet: 0,
      dayLength: 0
    }
  },
  methods: {
    calculateHourAngle: function (latitude, date) {
      let dayOfYear = this.calculateDayOfYear(date);
      console.log("day of year: " + dayOfYear)
      let sunDeclination = this.calculateSunDeclination(dayOfYear);
      console.log("sun declanation: " + sunDeclination)
      let latitudeRadians = latitude * Math.PI / 180;
      let cosHourAngle = -Math.tan(latitudeRadians) * Math.tan(sunDeclination);
      return Math.acos(cosHourAngle);
    },
    calculateSunDeclination: function (dayOfYear) {
      // equation from: https://www.sciencedirect.com/topics/engineering/solar-declination
      return 23.5 / 180 * Math.PI * Math.cos(2 * Math.PI * (dayOfYear - 172) / 365);
    },
    calculateDayOfYear: function (date) {
      // Code from https://stackoverflow.com/questions/8619879/javascript-calculate-the-day-of-the-year-1-366
      let start = new Date(date.getFullYear(), 0, 0);
      let diff = (date - start) + ((start.getTimezoneOffset() - date.getTimezoneOffset()) * 60 * 1000);
      let oneDay = 1000 * 60 * 60 * 24;
      return Math.floor(diff / oneDay);
    },
    calculateDayTimeInfo: function (latitude, longitude, date) {
      let hourAngle = this.calculateHourAngle(latitude, date)
      console.log("hour angle: " + hourAngle)
      let hoursFromNoon = hourAngle * 24 / (2 * Math.PI);
      console.log("hours from noon: " + hoursFromNoon)
      let sunRise = 12 - hoursFromNoon;
      let sunRiseHour = Math.floor(sunRise);
      let sunRiseDate = new Date(2000, 0, 0, sunRiseHour, (sunRise - sunRiseHour) * 60)

      let sunSet = 12 + hoursFromNoon;
      let sunSetHour = Math.floor(sunSet);
      let sunSetDate = new Date(2000, 0, 0, sunSetHour, (sunSet - sunSetHour) * 60)

      let dayLength = sunSetDate - sunRiseDate;
      return {
        sunRise: sunRiseDate,
        sunSet: sunSetDate,
        dayLength: dayLength
      }
    },
    displayDayLengthInfo: function (latitude, longitude, date) {

      let dayTimeInfo = this.calculateDayTimeInfo(latitude, longitude, date)
      console.log(dayTimeInfo);
      this.sunRise = dayTimeInfo.sunRise
      this.sunSet = dayTimeInfo.sunSet
      this.dayLength = dayTimeInfo.dayLength
    }
  },
  components: {
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
</style>
