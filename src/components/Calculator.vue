<template>
  <section id="main-section">
    <div id="info-area">
      <div id="upper-info">
        <h3>One day</h3>
        <a>To see detailed information about one day, select a date.</a>
        <input type="datetime-local" v-model="dateString">
        <br>
        <div id="results-container" v-if="dateString">
          <label v-if="oneDayInfo.polarDay">POLAR DAY</label>
          <label v-else-if="oneDayInfo.polarNight">POLAR NIGHT</label>

          <label v-if="oneDayInfo.sunriseStr">Sunrise: {{ oneDayInfo.sunriseStr }}</label>
          <br>
          <label v-if="oneDayInfo.sunsetStr">Sunset: {{ oneDayInfo.sunsetStr }}</label>
          <br>
          <label>Length of day: {{ oneDayInfo.dayLength }}</label>
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
            <label>{{startDate}}</label>
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
      let data = [
        //['Date', 'Length of day']
      ]
      for (let i = 0; i < diffInDays; i++) {
        let date = new Date(this.startDate)
        date.setDate(date.getDate() + i)
        //let times = SunCalc.getTimes(date, this.selectedCoords.lat, this.selectedCoords.lng)
        //let dayLength = (times.sunset - times.sunrise) / 1000 / 60 / 60
        let dayLength = this.calculateTimes(date, this.selectedCoords.lat, this.selectedCoords.lng).dayLength
        data.push([date, dayLength])
      }
      data.unshift(['Date', 'Length of day'])
      return data
    },
    oneDayInfo: function () {
      if (!this.dateString) {
        alert("Please select a date")
        return
      }
      if (!this.selectedCoords) {
        alert("Please specify coordinates")
        return;
      }

      let times = this.calculateTimes(new Date(this.dateString), this.selectedCoords.lat, this.selectedCoords.lng)
      if (!times.sunrise || !times.sunset) {
        return {
          sunriseStr: null,
          sunsetStr: null,
          dayLength: times.dayLength + 'h',
          polarDay: times.dayLength === 24,
          polarNight: times.dayLength === 0
        }
      }

      let sunriseStr = times.sunrise.getHours() + ':' + times.sunrise.getMinutes();
      let sunsetStr = times.sunset.getHours() + ':' + times.sunset.getMinutes();
      let dayLengthMinutes = times.dayLength * 60
      let dayLengthHours = Math.floor(dayLengthMinutes / 60);
      let dayLengthTime = dayLengthHours + 'h ' + Math.round(dayLengthMinutes - dayLengthHours * 60) + 'min';

      return {
        sunriseStr: sunriseStr,
        sunsetStr: sunsetStr,
        dayLength: dayLengthTime,
        polarDay: false,
        polarNight: false
      }
    },
  },
  props: {
    selectedCoords: Object,
  },
  methods: {
    calculateTimes: function (date, latitude, longitude) {
      let times = SunCalc.getTimes(date, latitude, longitude);
      if (isNaN(times.sunrise) || isNaN(times.sunset)) {
        // polar day or polar night
        const springEquinox = new Date(date.getFullYear() + '-03-20')
        const autumnEquinox = new Date(date.getFullYear() + '-09-23')
        let dayLength = null
        if (date < springEquinox || date > autumnEquinox) {
          if (latitude > 0) dayLength = 0
          else dayLength = 24
        }
        else if (date > springEquinox && date < autumnEquinox) {
          if (latitude > 0) dayLength = 24
          else dayLength = 0
        }
        return {
          sunrise: null,
          sunset: null,
          dayLength: dayLength
        }
      }

      return {
        sunrise: times.sunrise,
        sunset: times.sunset,
        dayLength: (times.sunset - times.sunrise) / 1000 / 60 / 60
      }
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