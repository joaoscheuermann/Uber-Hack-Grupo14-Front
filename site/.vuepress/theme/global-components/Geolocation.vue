<template>
  <div class="login">
    <h1>Condu</h1>
    <div><b>lat:</b> {{ position.lat }}</div>
    <div><b>long:</b> {{ position.lng }}</div>
    <div><b>timestamp:</b> {{ timestamp }}</div>

    <div v-if="geolocationPermitionState === 'denied'" class="btn" @click="handleGeolocationPermission"> enable geo location </div>
  </div>
</template>

<script>
export default {
  data () {
    return {
      geolocationPermitionState: null,
      geolocationWatcherID: null,
      position: {
        lat: null,
        lng: null
      },
      timestamp: null,
    }
  },

  methods: {
    handleGeolocationPermission () {
      navigator.permissions.query({name:'geolocation'})
      .then((result) => {
        this.geolocationPermitionState = result.state

        if (result.state == 'granted') {
          report(result.state);
          this.startGeolocationWatch();
        } else if (result.state == 'prompt') {
          report(result.state);
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
    },

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
    this.handleGeolocationPermission()
  }
}

</script>

<style lang="scss">
.btn {
  padding: 20px;
  margin: 30px 0 0 0;
  border: 1px solid black;
}
</style>