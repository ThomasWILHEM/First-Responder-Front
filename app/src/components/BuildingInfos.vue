<template>
  <div id="building-infos" @mousewheel.stop="" @dblclick.stop="">
    <div id="title">
      <label id="name">{{building.name}}</label>
      <label id="type">{{building.type.name}}</label>
    </div>
    <div id="infos">
      <div class="info">
        <div>
          <img src="../assets/images/address.png" alt="Address">
          <label :title="building.address">{{building.address}}</label>
        </div>
      </div>
      <div class="info">
        <div>
          <img src="../assets/images/staff.png" alt="Staff">
          <label>{{this.staffs.length}} staffs</label>
        </div>
        <img @click="toggleStaffList" class="more-infos" :src="staffInfosImgSrc" alt="Infos">
      </div>
      <div class="info">
        <div>
          <img src="../assets/images/vehicle.png" alt="Vehicles">
          <label>{{this.vehicles.length}} vehicles</label>
        </div>
        <img @click="toggleVehicleList" class="more-infos" :src="vehicleInfosImgSrc" alt="Infos">
      </div>
    </div>
    <div v-if="page !== ''">
      <div class="separator"></div>
      <VehicleList
          v-if="page === 'vehicle'"
          :vehicles-list="vehicles"
          :building="building"
      />
      <StaffList
          v-else-if="page === 'staff'"
          :staffs-list="staffs"
          :building="building"
      />
    </div>
  </div>

<!--  <div>
    <h1>{{building.name}}</h1>
    <h3>{{building.type.name}}</h3>
  </div>
  <div>
    <button @click="toggleStaffList">Staff</button>
    <button @click="toggleVehicleList">Vehicles</button>
  </div>
  <div>
&lt;!&ndash;    <VehicleList
      v-if="page === 'vehicle'"
      :vehicles-list="vehicles"
      :building="building"
    >
    </VehicleList>
    <StaffList
      v-else-if="page === 'staff'"
      :staffs-list="staffs"
      :building="building"
    >
    </StaffList>&ndash;&gt;
  </div>-->
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
      staffs: [],
      vehicleInfosImgSrc: "/src/assets/images/infos.png",
      staffInfosImgSrc: "/src/assets/images/infos.png"
    }
  },
  watch:{
    building: function (newBuilding, oldBuilding) {
      axios.get('http://127.0.0.1:8000/vehicles/from-building/' + this.building.id)
          .then(response => {
            this.vehicles = response.data.results;
          })
          .catch(error => {
            console.error('Error:', error);
          });

      axios.get('http://127.0.0.1:8000/staffs/from-building/' + this.building.id)
          .then(response => {
            this.staffs = response.data.results;
          })
          .catch(error => {
            console.error('Error:', error);
          });
      this.page = "";
    },
    page: function (newPage, oldPage){
      if(newPage === ""){
        this.vehicleInfosImgSrc = "/src/assets/images/infos.png"
        this.staffInfosImgSrc = "/src/assets/images/infos.png"
      }else if(newPage === "vehicle"){
        this.vehicleInfosImgSrc = "/src/assets/images/minus.png"
        this.staffInfosImgSrc = "/src/assets/images/infos.png"
      }else if(newPage === "staff"){
        this.vehicleInfosImgSrc = "/src/assets/images/infos.png"
        this.staffInfosImgSrc = "/src/assets/images/minus.png"
      }
    }
  },
  mounted() {
  },
  methods: {
    toggleVehicleList(){
      if(this.page === "vehicle"){
        this.page = ""
        this.vehicleInfosImgSrc = "/src/assets/images/infos.png"
      }else {
        this.page = "vehicle"
        this.vehicleInfosImgSrc = "/src/assets/images/minus.png"
      }
    },
    toggleStaffList(){
      if(this.page === "staff"){
        this.page = ""
        this.staffInfosImgSrc = "/src/assets/images/infos.png"
      }else {
        this.page = "staff"
        this.staffInfosImgSrc = "/src/assets/images/minus.png"
      }
    }
  }
};
</script>

<style scoped>
  #building-infos {
    position: absolute;
    top: 40px;
    left: 40px;
    background-color: white;
    padding: 20px;
    z-index: 1000;
    border-radius: 10px;
    border: 5px solid #651f20;
    width: 25lh;
  }

  #title{
    display: flex;
    flex-direction: column;
    background-color: #651f20;
    color: #f2f2f2;
    padding: 10px;
    border-radius: 10px;
  }

  #title #name{
    font-weight: bolder;
    font-size: x-large;
  }

  #title #type{
    font-weight: bold;
    font-size: medium;
  }

  .info{
    display: flex;
    justify-content: space-between;
    margin: 20px;
  }

  .info > div {
    display: flex;
    align-items: center;
  }

  .info img{
    width: 30px;
    height: 30px;
    margin-right: 15px;
  }

  .info img.more-infos{
    float: right;
  }

  .info img.more-infos:hover{
    cursor: pointer;
  }

  .info div{
    max-width: 20lh;
  }

  .info label{
    max-width: 20lh;
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;

    color: #651f20;
    font-weight: bolder;
    font-size: large;
  }

  .separator{
    height: 5px;
    background-color: #651f20;
  }
</style>