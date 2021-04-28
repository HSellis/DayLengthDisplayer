<template>
  <div>
    <input type="number" placeholder="Latitude" v-model="userEnteredLatitude">
    <input type="number" placeholder="Longitude" v-model="userEnteredLongitude">
    <input type="datetime-local" v-model="userEnteredDate">
    <button @click="displayInfo()">Display info</button>
  </div>
</template>

<script>
export default {
  name: "InfoInput",
  data: function () {
    return {
      userEnteredLatitude: null,
      userEnteredLongitude: null,
      userEnteredDate: null
    }
  },
  methods: {
    displayInfo: function () {
      if (!this.userEnteredLatitude) {
        alert("enter latitude")
        return
      }
      if (!this.userEnteredLongitude) {
        alert("enter longitude")
        return;
      }
      if (!this.userEnteredDate) {
        alert("enter date")
        return;
      }

      this.displayDayLengthInfo(this.userEnteredLatitude, this.userEnteredLongitude, new Date(this.userEnteredDate));
    }
  },
  props: {
    displayDayLengthInfo: Function
  }
}
</script>

<style scoped>

</style>