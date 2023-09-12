<template>
  <div id="map" style="height: 400px;"></div>
  <button @click="getCalls">Add call</button>
  <button @click="toogleAddBuilding">Add building</button>
  <div v-if="addBuilding">
      <h2>Building creation</h2>
      <form @submit.prevent="submitForm">
        <div class="form-group">
          <label for="coordinates_latitude">Latitude :</label>
          <input
              type="number"
              step="0.000001"
              id="coordinates_latitude"
              v-model="formData.coordinates_latitude"
          />
        </div>
        <div class="form-group">
          <label for="coordinates_longitude">Longitude :</label>
          <input
              type="number"
              step="0.000001"
              id="coordinates_longitude"
              v-model="formData.coordinates_longitude"
          />
        </div>
        <div class="form-group">
          <label for="type_id">Type :</label>
          <select id="type_id" v-model="formData.type_id">
            <option v-for="type in type_list" :value="type.id">{{ type.name }}</option>
          </select>
        </div>
        <button type="submit">Soumettre</button>
      </form>
  </div>
</template>

<script>
import L from 'leaflet';
import axios from 'axios';

export default {
  data() {
    return {
      map: null,
      callMarkersLayer: null,
      buildingMarkersLayer: null,
      addBuilding: false,
      formData: {
        coordinates_latitude: 0,
        coordinates_longitude: 0,
        type_id: 0,
      },
      type_list: null
    };
  },
  mounted() {
    this.map = L.map('map').setView([44.5833, -0.0333], 13);

    L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
      attribution: '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors'
    }).addTo(this.map);

    this.callMarkersLayer = L.layerGroup().addTo(this.map);
    this.buildingMarkersLayer = L.layerGroup().addTo(this.map);

    this.getBuildings();
  },
  methods: {
    getCalls() {
      axios.get('http://127.0.0.1:8000/calls/')
          .then(response => {
            const calls = response.data.results;
            console.log(calls);
            calls.forEach(call => {
              const marker = L.marker([call.coordinates_latitude, call.coordinates_longitude]);
              marker.bindPopup("  " +
                  "<div>\n" +
                  "    <h2>" + call.scenario.name + "</h2>\n" +
                  "    <p>" + call.scenario.description + "</p>\n" +
                  "  </div>");
              this.callMarkersLayer.addLayer(marker);
            });
          })
          .catch(error => {
            console.error('Error:', error);
          });
    },
    getBuildings() {
      axios.get('http://127.0.0.1:8000/buildings/')
          .then(response => {
            const buildings = response.data.results;
            buildings.forEach(building => {
              const marker = L.marker([building.coordinates_latitude, building.coordinates_longitude]);
              marker.bindPopup(building.type.name);
              this.buildingMarkersLayer.addLayer(marker);
            });
          })
          .catch(error => {
            console.error('Error:', error);
          });
    },
    toogleAddBuilding(){

      if (!this.addBuilding) {
        axios.get('http://127.0.0.1:8000/buildings-types/')
            .then(response => {
              this.type_list = response.data.results;
              this.addBuilding = !this.addBuilding;
            })
            .catch(error => {
              console.error('Error:', error);
            });
      }else{
        this.type_list = null;
        this.addBuilding = !this.addBuilding;
      }

    },
    submitForm() {
      axios
          .post('http://127.0.0.1:8000/buildings/', this.formData)
          .then((response) => {
            const building = response.data;
            const marker = L.marker([building.coordinates_latitude, building.coordinates_longitude]);
            marker.bindPopup(building.type.name);
            this.buildingMarkersLayer.addLayer(marker);
          })
          .catch((error) => {
            console.error('Erreur lors de la requête :', error);
          });
    },
  }
};
</script>

<style>
/* Style général du formulaire */
form {
  width: 300px;
  margin: 0 auto;
  padding: 20px;
  border: 1px solid #ccc;
  border-radius: 5px;
  background-color: #f9f9f9;
}

/* Style des étiquettes */
label {
  display: block;
  margin-bottom: 5px;
}

/* Style des champs de texte et des sélecteurs */
input[type="number"],
select {
  width: 100%;
  padding: 10px;
  margin-bottom: 15px;
  border: 1px solid #ccc;
  border-radius: 3px;
}

/* Style du bouton de soumission */
button[type="submit"] {
  background-color: #007bff;
  color: #fff;
  padding: 10px 20px;
  border: none;
  border-radius: 3px;
  cursor: pointer;
}

/* Style du bouton de soumission au survol */
button[type="submit"]:hover {
  background-color: #0056b3;
}
</style>