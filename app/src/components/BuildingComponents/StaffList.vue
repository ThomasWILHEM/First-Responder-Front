<template>
  <div v-for="staff in staffsList">
    {{ staff.firstname }} {{ staff.lastname }} - {{staff.type.name}}
  </div>
</template>

<script>

export default {
  props: {
    staffsList: Object,
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