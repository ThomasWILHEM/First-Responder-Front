<template>
  <div id="building-form">
    <h2>Add a building</h2>
    <form @submit.prevent="submitForm">
      <div class="form-group">
        <label for="name">Name :</label>
        <input
            type="text"
            id="name"
            v-model="formData.name"
        />
      </div>
      <div class="form-group">
        <label for="type_id">Type :</label>
        <select id="type_id" v-model="formData.type_id">
          <option v-for="type in typeList" :value="type.id">{{ type.name }}</option>
        </select>
      </div>
      <div class="form-group">
        <label for="coordinates">Coordinates :</label>
        <input
            type="text"
            id="coordinates_latitude"
            :value="formData.coordinates_latitude + ', ' + formData.coordinates_longitude"
            disabled
        />
      </div>
      <div class="form-group">
        <label for="coordinates_longitude">Address :</label>
        <input
            type="text"
            id="adress"
            v-model="formData.address"
            disabled
        />
      </div>

      <button type="submit">Add building</button>
    </form>
  </div>
</template>

<script>
export default {
  props: {
    formData: Object,
    typeList: Array
  },
  methods: {
    submitForm() {
      this.$emit('submit-form');
    },
  },
  mounted() {
  }
};
</script>

<style scoped>
#building-form {
  position: absolute;
  bottom: 100px;
  right: 20px;
  background-color: white;
  padding: 20px;
  z-index: 1000;
  border-radius: 10px;
  border: 5px solid #651f20;
}

.form-group{
  width: 95%;
  margin: 5px;
}

input{
  height: 25px;
}

select{
  height: 32px;
}

h2{
  color: #88282A;
  font-family: "Inter Semi Bold","Arial Black";
  text-align: center;
  font-size: x-large;
  margin: 0;
  margin-bottom: 20px;
}
/* Style des étiquettes */
label {
  display: block;
  margin-bottom: 5px;
}



/* Style du bouton de soumission */
button[type="submit"] {
  margin: 15px;
  background-color: #88282A;
  color: #fff;
  padding: 10px 20px;
  border: none;
  border-radius: 3px;
  cursor: pointer;
}

form{
  display: flex;
  flex-direction: column;
  align-items: center;
}

/* Style du bouton de soumission au survol */
button[type="submit"]:hover {
  background-color: #651f20;
}
</style>