---

title: 'João Scheuermann'
description: ''

---

<template>
  <div class="login">
    <div>{{ position }}</div>
    <div>{{ timestamp }}</div>
    <div>{{ dump }}</div>
  </div>
</template>

<script>
export default {
  data () {
    return {
      geolocationWatcherID: null,
      position: null,
      timestamp: null,
      dump: null,
    }
  },

  methods: {
    startGeolocationWatch () {
      const GEOLOCATION_OPTIONS = {enableHighAccuracy: true}
      this.geolocationWatcherID = navigator.geolocation.watchPosition(this.watchPositionSucces, this.watchPositionError, GEOLOCATION_OPTIONS)
    },

    watchPositionSucces ({ coords, timestamp }) {
      this.position = JSON.stringify(coords)
    },

    watchPositionError (error) {

    },
  },
  
  mounted () {
    this.startGeolocationWatch()
  }
}

</script>

<style lang="scss">

</style>