<template>
  <div>
    <h1>{{building.name}}</h1>
    <h3>{{building.type.name}}</h3>
  </div>
  <div>
    <button @click="toggleStaffList">Staff</button>
    <button @click="toggleVehicleList">Vehicles</button>
  </div>
  <div>
    <VehicleList
      v-if="page === 'vehicle'"
      :vehicles-list="vehicles"
    >
    </VehicleList>
    <StaffList
      v-else-if="page === 'staff'"
      :staffs-list="staffs"
    >
    </StaffList>
  </div>
</template>

<script>
import VehicleList from "./BuildingComponents/VehicleList.vue";
import StaffList from "./BuildingComponents/StaffList.vue";
import axios from "axios";
import L from "leaflet";

export default {
  components: {StaffList, VehicleList},
  props: {
    building: Object,
  },
  data(){
    return {
      page: "",
      vehicles: [],
      staffs: []
    }
  },
  methods: {
    toggleVehicleList(){
      axios.get('http://127.0.0.1:8000/vehicles/from-building/' + this.building.id)
          .then(response => {
            this.vehicles = response.data.results;
          })
          .catch(error => {
            console.error('Error:', error);
          });
      this.page = "vehicle"
    },
    toggleStaffList(){
      axios.get('http://127.0.0.1:8000/staffs/from-building/' + this.building.id)
          .then(response => {
            this.staffs = response.data.results;
          })
          .catch(error => {
            console.error('Error:', error);
          });
      this.page = "staff"

    }
  }
};
</script>

<style scoped>

</style>