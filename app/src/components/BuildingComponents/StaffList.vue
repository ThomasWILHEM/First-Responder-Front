<template>
  <div id="staff-list">
    <div id="title">
      <label>{{ title }}</label>
      <img @click="toggleStaffCreation" :src="imageSrc" alt="Add a vehicle">
    </div>
    <div v-if="!isStaffCreation" id=list>
      <div v-for="staff in staffsList" class="staff">
        <label>
          {{ staff.firstname }} {{ staff.lastname }} - {{staff.type.name}}
        </label>
      </div>
    </div>
    <staff-creation-form
        v-else
        :building="building"
        :staff-list="staffsList"
        @create-staff="createStaff"
    />
  </div>
</template>

<script>


import axios from "axios";
import StaffCreationForm from "./StaffCreationForm.vue";

export default {
  components: {StaffCreationForm},
  props: {
    staffsList: Array,
    building: Object,
  },
  data(){
    return {
      isStaffCreation: false,
      title: "Staff List",
      imageSrc: "src/assets/images/plus.png"
    }
  },
  methods: {
    toggleStaffCreation(){
      if (this.isStaffCreation)
      {
        this.title = "Staff List"
        this.imageSrc = "src/assets/images/plus.png"
        this.isStaffCreation = false;
      }else{
        this.title = "Add a staff"
        this.imageSrc = "src/assets/images/white-minus.png"
        this.isStaffCreation = true;
      }
    },
    createStaff(staff) {
      console.log(staff);
      axios.post('http://127.0.0.1:8000/staffs/', staff)
          .then(response => {
            this.staffsList.push(response.data);
            this.isStaffCreation = false;
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
  height: 1.5lh;
}

.staff label{
  align-content: center;
}

label{
  margin: 0;
}
</style>