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
    
    <div><b>deltaTime:</b> {{ deltaTime }}</div>
    <div><b>distance</b> {{ distance }}</div>
    <div><b>timestamp:</b> {{ lastPosition.timestamp }}</div>
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
        var R = 6371; // Radius of the earth in km
        var dLat = deg2rad(lat2-lat1);  // deg2rad below
        var dLon = deg2rad(lon2-lon1); 
        var a = 
          Math.sin(dLat/2) * Math.sin(dLat/2) +
          Math.cos(deg2rad(lat1)) * Math.cos(deg2rad(lat2)) * 
          Math.sin(dLon/2) * Math.sin(dLon/2)
          ; 
        var c = 2 * Math.atan2(Math.sqrt(a), Math.sqrt(1-a)); 
        var d = R * c; // Distance in km
        return d;
      }

      function deg2rad(deg) {
        return deg * (Math.PI/180)
      }

      let deltaTime = Math.trunc(timestampInSeconds(current.timestamp) - timestampInSeconds(last.timestamp))
      let distanceBetweenPoints = getDistanceFromLatLonInKm(last.lat, last.lng, current.lat, current.lng) * 1000 // in meters
      
      this.deltaTime = deltaTime
      this.distance = distanceBetweenPoints
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