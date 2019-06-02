<template>
  <div class="login">
    <h1>Condu</h1>

    <h3>Current</h3>
    <div><b>lat:</b> {{ currentPosition.lat }}</div>
    <div><b>long:</b> {{ currentPosition.lng }}</div>
    <div><b>timestamp:</b> {{ currentPosition.timestamp }}</div>
    <br>
    <h3>Last</h3>
    <div><b>lat:</b> {{ lastPosition.lat }}</div>
    <div><b>long:</b> {{ lastPosition.lng }}</div>
    <div><b>timestamp:</b> {{ lastPosition.timestamp }}</div>
    <br>
    
    <div><b>time:</b> {{ deltaTime }}</div>
    <div><b>distance</b> {{ distance }}</div>
    <div><b>speed:</b> {{ speed }}</div>
  </div>
</template>

<script>
export default {
  data () {
    return {
      geolocationPermitionState: null,
      geolocationWatcherID: null,
      
      currentPosition: {
        timestamp: null,
        lat: null,
        lng: null,
      },
      lastPosition: {
        timestamp: null,
        lat: null,
        lng: null
      },

      speed: 0,
      deltaTime: 0,
      distance: 0
    }
  },

  methods: {
    startGeolocationWatch () {
      const GEOLOCATION_OPTIONS = {enableHighAccuracy: true}
  
      this.geolocationWatcherID = navigator.geolocation.watchPosition(this.watchPositionSucces, this.watchPositionError, GEOLOCATION_OPTIONS)
    },

    calculateSpeed (last, current) {
      function timestampInSeconds (timestamp) {
        return timestamp / 1000
      }

      function getDistanceFromLatLonInKm(lat1,lon1,lat2,lon2) {
        var p = 0.017453292519943295;    // Math.PI / 180
        var c = Math.cos;
        var a = 0.5 - c((lat2 - lat1) * p)/2 + 
                c(lat1 * p) * c(lat2 * p) * 
                (1 - c((lon2 - lon1) * p))/2;

        return 12742 * Math.asin(Math.sqrt(a)); // 2 * R; R = 6371 km
      }

      function deg2rad(deg) {
        return deg * (Math.PI/180)
      }

      let deltaTime = Math.trunc(timestampInSeconds(current.timestamp) - timestampInSeconds(last.timestamp))
      let distanceBetweenPoints = getDistanceFromLatLonInKm(last.lat, last.lng, current.lat, current.lng) * 1000 // in meters
      
      this.deltaTime = deltaTime
      this.distance = distanceBetweenPoints
      this.speed = ((distanceBetweenPoints / deltaTime) * 3.6).toFixed(1)
    },

    watchPositionSucces (data) {
      const { coords, timestamp } = data
      const { latitude, longitude } = coords

      this.lastPosition = Object.assign(this.lastPosition, this.currentPosition)

      this.currentPosition.lat = latitude
      this.currentPosition.lng = longitude
      this.currentPosition.timestamp = timestamp

      if (!this.lastPosition.timestamp) Object.assign(this.lastPosition, this.currentPosition)

      this.calculateSpeed(this.lastPosition, this.currentPosition)
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
.btn {
  padding: 20px;
  margin: 30px 0 0 0;
  border: 1px solid black;
}
</style>