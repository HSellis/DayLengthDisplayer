<template>
  <section id="main-section">
    <div id="info-area">
      <div id="upper-info">
        <h3>One day</h3>
        <a>To see detailed information about one day, select a date.</a>
        <input type="datetime-local" v-model="dateString">
        <br>
        <button @click="calculateTimeInfo">Calculate</button>
        <div id="results-container">
          <label>Sunrise: {{ sunRise }}</label>
          <br>
          <label>Sunset: {{ sunSet }}</label>
          <br>
          <label>Length of day: {{ dayLength }}</label>
          <br>
        </div>
      </div>
      <div id="lower-info">
        <h3>Period of time</h3>
        <a>To visualize the length of day over a period of time, select a time period.</a>
        <div id="time-period-selecting">
          <div>
            <label>Start date: </label>
            <input type="datetime-local" v-model="startDate">
          </div>
          <div>
            <label>End date: </label>
            <input type="datetime-local" v-model="endDate">
          </div>
        </div>
      </div>
    </div>

    <div id="graph-area">
      <GChart
          v-if="startDate && endDate"
          id="chart"
          type="AreaChart"
          :data="chartData"
          :options="chartOptions"
      />
    </div>
  </section>
</template>

<script>
import {GChart} from 'vue-google-charts'

const SunCalc = require('suncalc');

export default {
  name: "Calculator",
  data: () => {
    return {
      dateString: null,

      sunRise: null,
      sunSet: null,
      dayLength: null,

      startDate: null,
      endDate: null,

      chartOptions: {
        title: 'Day length visualization',
        hAxis: {title: 'Date',  titleTextStyle: {color: '#333'}},
        vAxis: {title: 'Day length in hours'}
      }
    }
  },
  computed: {
    chartData: function () {
      let diffInDays = (new Date(this.endDate) - new Date(this.startDate)) / 1000 / 60 / 60 / 24;
      console.log("diff: " + diffInDays)
      let data = [
        //['Date', 'Length of day']
      ]
      for (let i = 0; i < diffInDays; i++) {
        let date = new Date(this.startDate)
        date.setDate(date.getDate() + i)
        let times = SunCalc.getTimes(date, this.selectedCoords.lat, this.selectedCoords.lng)
        let dayLength = (times.sunset - times.sunrise) / 1000 / 60 / 60
        data.push([date, dayLength])
      }
      data.unshift(['Date', 'Length of day'])
      console.log(data)
      return data
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
  },
  components: {
    GChart
  }
}
</script>

<style scoped>

#main-section {
  height: 100%;
  width: 100%;
  display: flex;
  justify-content: left;
}

#main-section > div {
  border: solid 3px black;
}

#info-area {
  width: 25%;
  justify-content: center;
}

#info-area > div {
  margin: 5px;
  border: 2px solid lightgray;
  width: 95%;
}

#upper-info {
  height: 50%;
}

#lower-info {
  height: 50%;
}

#graph-area {
  width: 75%;
}

#chart {
  width: 100%;
  height: 100%;
}

</style>