<template>
  <div id="staff-creation">
    <form @submit.prevent="createStaff">
      <div class="form-group">
        <label for="firstname">Firstame :</label>
        <input
            type="text"
            id="firstname"
            v-model="staff.firstname"
        />
      </div>
      <div class="form-group">
        <label for="lastname">Lastname :</label>
        <input
            type="text"
            id="lastname"
            v-model="staff.lastname"
        />
      </div>
      <div class="form-group">
        <label for="type_id">Type </label>
        <select id="type_id" v-model="staff.type_id">
          <option v-for="type in staffTypes" :value="type.id">{{ type.name }}</option>
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
      staff: {
        firstname: "",
        lastname: "",
        type_id: 0,
        vehicle_id: null,
        building_id: 0
      },
      staffTypes: []
    };
  },
  mounted() {
    axios.get('http://127.0.0.1:8000/api/staff-types')
       .then(response => {
         this.staffTypes = response.data;
         this.staff.building_id = this.building.id;
       })
       .catch(error => {
         console.error('Error:', error);
       });
  },
  methods: {
    createStaff() {
      this.$emit('create-staff', this.staff);
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

#staff-creation{
  margin: 10px;
}
</style>
