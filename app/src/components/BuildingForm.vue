<template>
  <div>
    <h2>Building creation</h2>
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
        <label for="coordinates_latitude">Latitude :</label>
        <input
            type="number"
            step="0.00000001"
            id="coordinates_latitude"
            v-model="formData.coordinates_latitude"
            disabled
        />
      </div>
      <div class="form-group">
        <label for="coordinates_longitude">Longitude :</label>
        <input
            type="number"
            step="0.00000001"
            id="coordinates_longitude"
            v-model="formData.coordinates_longitude"
            disabled
        />
      </div>
      <div class="form-group">
        <label for="type_id">Type :</label>
        <select id="type_id" v-model="formData.type_id">
          <option v-for="type in typeList" :value="type.id">{{ type.name }}</option>
        </select>
      </div>
      <button type="submit">Create building</button>
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
    console.log(this.$props.typeList);
  }
};
</script>

<style scoped>
/* Styles CSS spécifiques au composant */
</style>