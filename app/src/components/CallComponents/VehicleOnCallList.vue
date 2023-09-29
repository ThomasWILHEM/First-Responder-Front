<template>
  <div id="staff-list">
    <div id="title">
      <label>{{ title }}</label>
      <img @click="toggleVehicleAddition" :src="imageSrc" alt="Add a vehicle">

    </div>
    <div v-if="!isVehicleAddition" id=list>
      <div v-for="vehicle in vehiclesOnScene" class="staff">
        <label>
          {{vehicle.type.name}} - {{vehicle.building.name}}
        </label>
      </div>
    </div>
    <div v-else>
      <div v-if="otherVehicles.length === 0" class="vehicleList">
        <label>No vehicles</label>
      </div>
      <div v-else class="vehicleList">
        <div  v-for="vehicle in otherVehicles" class="staff">
          <label class="vehiclesToChoose">
            <input type="checkbox" class="checkVehicle" v-model="checkedVehicles[vehicle.id]">
            {{vehicle.type.name}} - {{vehicle.building.name}}
          </label>
        </div>
        <button id="sendVehicles" @click.prevent="sendVehicles">Send</button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    vehiclesOnScene: Array,
    otherVehicles: Array
  },
  data(){
    return {
      checkedVehicles: {},
      isVehicleAddition: false,
      imageSrc: "src/assets/images/plus.png",
      title: "Vehicle On Scene",
    }
  },
  methods: {
    toggleVehicleAddition(){
      if (this.isVehicleAddition)
      {
        this.title = "Vehicle On Scene"
        this.imageSrc = "src/assets/images/plus.png"
        this.isVehicleAddition = false;
      }else{
        this.title = "Send vehicles"
        this.imageSrc = "src/assets/images/white-minus.png"
        this.isVehicleAddition = true;
      }
    },
    sendVehicles(){
      const selectedVehicles = [];
      for (const vehicleId in this.checkedVehicles) {
        if (this.checkedVehicles[vehicleId]) {
          selectedVehicles.push(parseInt(vehicleId));
        }
      }
      this.$emit('send-vehicles', selectedVehicles);
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

  .vehiclesToChoose{
    display: flex;
    align-items: center;
  }
  .checkVehicle{
    margin-right: 20px;
  }

  #sendVehicles{
    border: none;
    outline: none;
    cursor: pointer;
    width: 65px;
    height: 65px;
    background-color: #88282A;
    border-radius: 10%;
    color: white;
    display: flex;
    align-items: center;
    justify-content: center;
  }

  #sendVehicles:hover{
    background-color: #651f20;
  }

  .vehicleList{
    display: flex;
    flex-direction: column;
    justify-content: center;
  }

  .vehicleList > label {
    color: #651f20;
    font-weight: bolder;
    font-size: large;
  }

</style>