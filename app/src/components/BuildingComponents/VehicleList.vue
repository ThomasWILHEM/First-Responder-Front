<template>
  <button @click="toggleVehicleCreation">Buy vehicle</button>
  <div v-for="vehicle in vehiclesList">
    {{ vehicle.type.name }}
  </div>
  <vehicle-creation-form
      v-if="isVehicleCreation"
      :building="building"
  >
  </vehicle-creation-form>
</template>

<script>
import VehicleCreationForm from "./VehiclePartComponents/VehicleCreationForm.vue";

export default {
  components: {VehicleCreationForm},
  props: {
    vehiclesList: Object,
    building: Object,
  },
  data(){
    return {
      isVehicleCreation: false
    }
  },
  methods: {
    toggleVehicleCreation(){
      this.isVehicleCreation = !this.isVehicleCreation;
    }
  }
};
</script>

<style scoped>

</style>