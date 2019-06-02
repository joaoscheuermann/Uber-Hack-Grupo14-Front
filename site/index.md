---

title: 'João Scheuermann'
description: ''

---

<template>
  <div class="login">
    <h1>Condu</h1>
    <div><b>lat:</b> {{ position.lat }}</div>
    <div><b>long:</b> {{ position.lng }}</div>
    <div><b>timestamp:</b> {{ timestamp }}</div>
    <div>{{ dump }}</div>
  </div>
</template>

<script>
export default {
  data () {
    return {
      geolocationWatcherID: null,
      position: {
        lat: null,
        lng: null
      },
      timestamp: null,
      dump: null,
    }
  },

  methods: {
    startGeolocationWatch () {
      const GEOLOCATION_OPTIONS = {enableHighAccuracy: true}

      this.geolocationWatcherID = navigator.geolocation.watchPosition(this.watchPositionSucces, this.watchPositionError, GEOLOCATION_OPTIONS)
    },

    watchPositionSucces (data) {
      const { coords, timestamp } = data
      const { latitude, longitude } = coords

      this.position.lat = latitude
      this.position.lng = longitude
      this.timestamp = timestamp
    },

    watchPositionError (error) {

    },
  },
  
  mounted () {
    navigator.permissions.query({name:'geolocation'})
      .then((result) => {
        if (result.state == 'granted') {
          report(result.state);
        } else if (result.state == 'prompt') {
          report(result.state);
          this.startGeolocationWatch()
        } else if (result.state == 'denied') {
          report(result.state);
        }
        result.onchange = function() {
          report(result.state);
        }
      });

    function report(state) {
      alert('Permission ' + state);
    }
  }
}

</script>

<style lang="scss">

</style>