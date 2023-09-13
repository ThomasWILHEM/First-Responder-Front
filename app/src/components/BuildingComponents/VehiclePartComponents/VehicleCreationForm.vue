<template>
  <div>
    <h2>Créer un nouveau véhicule</h2>
    <form @submit.prevent="createVehicle">
      <div class="form-group">
        <label for="latitude">Latitude:</label>
        <input type="number" id="latitude" :value="building.coordinates_latitude" disabled>
      </div>
      <div class="form-group">
        <label for="longitude">Longitude:</label>
        <input type="number" id="longitude" :value="building.coordinates_longitude" disabled>
      </div>
      <div class="form-group">
        <label for="type_id">Type :</label>
        <select id="type_id" v-model="vehicle.type_id">
          <option v-for="type in vehicleTypes" :value="type.id">{{ type.name }}</option>
        </select>
      </div>
      <div class="form-group">
        <label for="building_id">Building:</label>
        <input type="text" id="building" :value="building.name" disabled>
      </div>
      <button type="submit">Buy vehicle</button>
    </form>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  props: {
    building: Object,
  },
  data() {
    return {
      vehicle: {
        coordinates_latitude: 0,
        coordinates_longitude: 0,
        type_id: 0,
        call_id: null,
        building_id: 0
      },
      vehicleTypes: []
    };
  },
  mounted() {
    axios.get('http://127.0.0.1:8000/vehicles-types/')
       .then(response => {
         this.vehicleTypes = response.data.results;
         this.vehicle.building_id = this.building.id;
       })
       .catch(error => {
         console.error('Error:', error);
       });
  },
  methods: {
    createVehicle() {
      axios.post('http://127.0.0.1:8000/vehicles/', this.vehicle)
        .then(response => {
          console.log(response.data);
        })
        .catch(error => {
          console.error('Error:', error);
        });

      this.vehicle = {
        coordinates_latitude: 0,
        coordinates_longitude: 0,
        type_id: 0,
        call_id: null,
        building_id: 0
      };
    }
  }
};
</script>

<style scoped>
/* Ajoutez du CSS pour styliser votre formulaire si nécessaire. */
</style>
