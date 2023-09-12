<template>
  <div v-for="vehicle in vehiclesList">
    {{ vehicle.id }}
  </div>
</template>

<script>
export default {
  props: {
    vehiclesList: Object,
  },
  data(){
    return {

    }
  },
  methods: {

  }
};
</script>

<style scoped>

</style>