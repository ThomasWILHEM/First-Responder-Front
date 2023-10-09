<template>
  <div id="building-infos" @mousewheel.stop="" @dblclick.stop="">
    <img id="closeButton" @click="closeCallInfos" src="../assets/images/close.png" alt="Close infos">
    <div id="title">
      <label id="name">{{call.scenario.name}}</label>
      <label id="type">{{call.scenario.description}}</label>
    </div>
    <div id="infos">
      <div class="info">
        <div>
          <img src="../assets/images/address.png" alt="Address">
<!--          <label :title="building.address">{{building.address}}</label>-->
              <label>{{call.coordinates_latitude}}, {{call.coordinates_longitude}}</label>
        </div>
      </div>
    </div>
    <div>
      <div class="separator"></div>
      <VehicleOnCallList
          v-if="page === 'listOnScene'"
          :vehicles-on-scene="vehiclesOnCall"
          :other-vehicles="otherVehicles"
          @send-vehicles="sendVehicles"
      />
    </div>
  </div>
</template>

<script>
import axios from "axios";
import VehicleOnCallList from "./CallComponents/VehicleOnCallList.vue";

export default {
  components: {VehicleOnCallList},
  props: {
    call: Object,
  },
  data(){
    return {
      vehiclesOnCall: [],
      otherVehicles: [],
      vehicleInfosImgSrc: "/src/assets/images/infos.png",
      page: "listOnScene"
    }
  },
  watch:{
    call: function (newCall, oldCall) {
      this.getVehicles();
    },
  },
  mounted() {
    this.getVehicles();
  },
  methods: {
    closeCallInfos(){
      this.$emit('close-infos');
    },
    getVehicles(){
      axios.get('http://127.0.0.1:8000/api/calls/' + this.call.id + '/ressources')
          .then(response => {
            console.log(response.data);
            this.vehiclesOnCall = response.data.vehicles_on_scene;
            this.otherVehicles = response.data.vehicles_available;
          })
          .catch(error => {
            console.error('Error:', error);
          });
    },
    sendVehicles(vehiclesToSend){
      console.log(vehiclesToSend)
      const data = {
        "vehicles": vehiclesToSend
      }
      axios.post('http://127.0.0.1:8000/vehicles/send_multiple_to_call?call_id=' + this.call.id, data)
          .then(response => {
            if(response.status === 200) {
              vehiclesToSend.forEach(vehicleId => {
                const index = this.otherVehicles.findIndex(vehicle => vehicle.id === vehicleId);
                const removedVehicle = this.otherVehicles.splice(index, 1)[0];
                this.vehiclesOnCall.push(removedVehicle);
              });



            }else{
              console.log("ERROR");
            }
          })
          .catch(error => {
            console.error('Error:', error);
          });
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

  #closeButton{
    position: absolute;
    top: -20px;
    right: -20px;
    width: 40px;
    height: 40px;
  }
  #closeButton:hover{
    cursor: pointer;
  }

</style>