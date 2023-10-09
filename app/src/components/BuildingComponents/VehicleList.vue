<template>
  <div id="staff-list">
    <div id="title">
      <label>{{ title }}</label>
      <img @click="toggleVehicleCreation" :src="imageSrc" alt="Add a vehicle">
    </div>
    <div v-if="!isVehicleCreation" id=list>
      <div v-for="vehicle in vehiclesList" class="staff">
        <label>
          {{vehicle.name}} - {{vehicle.type.name}}
        </label>
        <div class="occupants">
          <img src="../../assets/images/white-staff.png" alt="Address">
          <label>3</label>
        </div>
      </div>
    </div>
    <vehicle-creation-form
        v-else
        :building="building"
        :vehicle-list="vehiclesList"
        @create-vehicle="createVehicle"
    />
  </div>
</template>

<script>
import VehicleCreationForm from "./VehicleCreationForm.vue";
import axios from "axios";

export default {
  components: {VehicleCreationForm},
  props: {
    vehiclesList: Array,
    building: Object,
  },
  data(){
    return {
      isVehicleCreation: false,
      title: "Vehicle List",
      imageSrc: "src/assets/images/plus.png"
    }
  },
  methods: {
    toggleVehicleCreation(){
      if (this.isVehicleCreation)
      {
        this.title = "Vehicle List"
        this.imageSrc = "src/assets/images/plus.png"
        this.isVehicleCreation = false;
      }else{
        this.title = "Add a vehicle"
        this.imageSrc = "src/assets/images/white-minus.png"
        this.isVehicleCreation = true;
      }
    },
    createVehicle(vehicle) {
      axios.post('http://127.0.0.1:8000/api/vehicles', vehicle)
          .then(response => {
            this.vehiclesList.push(response.data);
            this.isVehicleCreation = false;
          })
          .catch(error => {
            console.error('Error:', error);
          });
    }
  }
};
</script>

<style scoped>
  #staff-list{
    margin-top: 20px;
    background-color: #D9D9D9;
    border-radius: 10px;
    max-height: 20lh;
  }

  #list{
    max-height: 16lh;
    overflow-y: auto;
  }

  #title{
    display: flex;
    flex-direction: row;
    background-color: #651f20;
    color: #f2f2f2;
    padding: 10px 20px;
    border-radius: 10px;
    justify-content: space-between;
    align-items: center;
  }

  #title label{
    font-weight: bolder;
    font-size: x-large;
  }

  .staff{
    display: flex;
    flex-direction: row;
    background-color: #651f20;
    color: #f2f2f2;
    margin: 10px;
    border-radius: 10px;
    align-items: center;
    justify-content: space-between;
    padding: 5px 20px;
    font-weight: bold;
  }

  .occupants{
    display: flex;
    align-items: center;
  }

  .occupants img{
    height: 1.5lh;
    margin-right: 10px;
  }

  .occupants label {
    font-weight: bold;
    font-size: medium;
  }

</style>