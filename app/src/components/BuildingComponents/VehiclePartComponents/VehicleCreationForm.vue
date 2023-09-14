<template>
  <div id="vehicle-creation">
    <form @submit.prevent="createVehicle">
      <div class="form-group">
        <label for="type_id">Type </label>
        <select id="type_id" v-model="vehicle.type_id">
          <option v-for="type in vehicleTypes" :value="type.id">{{ type.name }}</option>
        </select>
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
    vehiclesList: Object,
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
      this.$emit('create-vehicle', this.vehicle);
    }
  }
};
</script>

<style scoped>
.form-group{
  display: flex;
  flex-direction: column;
  width: 50%;
  margin: 5px;
}

.form-group label{
  color: #651f20;
  font-size: large;
  font-weight: bold;
}

#vehicle-creation{
  margin: 10px;
}
</style>
