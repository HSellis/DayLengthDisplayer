<template>
  <section id="main-section">
    <div id="info-area">
      <div id="upper-info">
        <h3>Üks päev</h3>
        <a>Ühe päeva kohta täpsema info nägemiseks vali kuupäev.</a>
        <div class="time-period-selecting">
          <input type="datetime-local" v-model="dateString">
        </div>
        <div id="results-container" v-if="dateString">
          <div v-if="oneDayInfo.polarDay">
            <label>POLAARPÄEV</label>
          </div>
          <div v-else-if="oneDayInfo.polarNight">
            <label>POLAARÖÖ</label>
          </div>
          <div v-else>
            <label>Päikesetõus: {{ oneDayInfo.sunriseStr }}</label>
            <br>
            <label>Päikeseloojang: {{ oneDayInfo.sunsetStr }}</label>
          </div>
          <label>Päeva pikkus: {{ oneDayInfo.dayLength }}</label>
          <br>
        </div>
      </div>
      <div id="lower-info">
        <h3>Ajaperiood</h3>
        <a>Et visualiseerida päeva pikkuse muutumist ajas, vali ajaperiood.</a>
        <div class="time-period-selecting">
          <div>
            <label>Alguskuupäev: </label>
            <input type="datetime-local" v-model="startDate">
          </div>
          <div>
            <label>Lõppkuupäev: </label>
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
        title: 'Päeva pikkuse muutumine ajas',
        hAxis: {title: 'Kuupäev'},
        vAxis: {title: 'Päeva pikkus tundides'}
      }
    }
  },
  computed: {
    chartData: function () {
      let diffInDays = (new Date(this.endDate) - new Date(this.startDate)) / 1000 / 60 / 60 / 24;
      let data = []
      // Iga päeva kohta arvuta päeva pikkus
      for (let i = 0; i < diffInDays; i++) {
        let date = new Date(this.startDate)
        date.setDate(date.getDate() + i)
        let dayLength = this.calculateTimes(date, this.selectedCoords.lat, this.selectedCoords.lng).dayLength
        data.push([date, dayLength])
      }
      data.unshift(['Kuupäev', 'Päeva pikkus'])
      return data
    },
    oneDayInfo: function () {
      if (!this.dateString) {
        alert("Palun vali kuupäev")
        return
      }
      if (!this.selectedCoords) {
        alert("Palun määra koordinaadid")
        return;
      }

      let times = this.calculateTimes(new Date(this.dateString), this.selectedCoords.lat, this.selectedCoords.lng)
      if (!times.sunrise || !times.sunset) {
        // polaarpäev või polaaröö
        return {
          sunriseStr: null,
          sunsetStr: null,
          dayLength: times.dayLength + 'h',
          polarDay: times.dayLength === 24,
          polarNight: times.dayLength === 0
        }
      }

      let sunriseMin = times.sunrise.getMinutes()
      if (sunriseMin < 10) sunriseMin = '0' + sunriseMin
      let sunriseStr = times.sunrise.getHours() + ':' + sunriseMin;

      let sunsetMin = times.sunset.getMinutes()
      if (sunsetMin < 10) sunsetMin = '0' + sunsetMin
      let sunsetStr = times.sunset.getHours() + ':' + sunsetMin;

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
        // polaarpäev või polaaröö
        const springEquinox = new Date(date.getFullYear() + '-03-20')
        const autumnEquinox = new Date(date.getFullYear() + '-09-23')
        let dayLength = null
        if (date < springEquinox || date > autumnEquinox) {
          // lähemal talvisele pööripäevale kui suvisele (põhjapoolkeral)
          if (latitude > 0) dayLength = 0
          else dayLength = 24
        }
        else if (date > springEquinox && date < autumnEquinox) {
          // lähemal suvisele pööripäevale kui talvisele (põhjapoolkeral)
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

h3 {
  margin: 5px;
}

#main-section > div {
  border: solid 2px black;
}

#info-area {
  width: 25%;
  justify-content: center;
}

#info-area > div {
  margin: 5px;
  border: 2px solid lightgray;
  width: auto;
  height: 47%;
  padding: 0 5px 0 5px;
}

.time-period-selecting {
  margin: 5px 10% 5px 10%;
  width: auto;
}

.time-period-selecting div {
  float: right;
}

#results-container {
  background-color: #cde2ff;
  border: 2px groove #70a7fc;
  margin: 3% 15% 3% 15%;
  width: auto;
}

#graph-area {
  width: 75%;
}

#chart {
  width: 100%;
  height: 100%;
}

</style>