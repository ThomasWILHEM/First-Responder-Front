<template>
  <div id="map" style="height: 400px;"></div>
  <button @click="addCall">Add call</button>
</template>

<script>
import L from 'leaflet';
import axios from 'axios';

export default {
  data() {
    return {
      map: null,
      markersLayer: null,
    };
  },
  mounted() {
    this.map = L.map('map').setView([44.5833, -0.0333], 13);

    L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
      attribution: '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors'
    }).addTo(this.map);

    this.markersLayer = L.layerGroup().addTo(this.map);
  },
  methods: {
    addCall() {
      axios.get('http://127.0.0.1:8000/calls/')
          .then(response => {
            const calls = response.data.results;
            console.log(calls);
            calls.forEach(call => {
              const marker = L.marker([call.coordinates_latitude, call.coordinates_longitude]);
              marker.bindPopup(call.scenario.name);
              this.markersLayer.addLayer(marker);
            });
          })
          .catch(error => {
            console.error('Error:', error);
          });
    }
  }
};
</script>

<style>
</style>