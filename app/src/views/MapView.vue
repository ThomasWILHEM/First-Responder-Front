<template>
  <div id="page">
    <div id="actions">
      <div id="map">
        <div class="map-controls">
          <button @click="toggleAddBuilding">
            <img src="../assets/images/plus.png" alt="Add building">
          </button>
        </div>

        <building-form
            v-if="addBuilding"
            :form-data="formData"
            :type-list="typeList"
            @submit-form="submitForm">
        </building-form>

        <building-infos
            v-if="selectedBuilding != null"
            :building="selectedBuilding"
            @close-infos="closeBuildingInfos"
        />

        <call-infos
            v-if="selectedCall != null"
            :call="selectedCall"
            @close-infos="closeCallInfos"
        />
      </div>
    </div>
  </div>
  <button @click="createCall">
    Spawn Call
  </button>
</template>

<script>
import L from 'leaflet';
import axios from 'axios';
import BuildingForm from "../components/BuildingForm.vue";
import BuildingInfos from "../components/BuildingInfos.vue";
import CallInfos from "../components/CallInfos.vue";

export default {
  components: {CallInfos, BuildingInfos, BuildingForm},
  data() {
    return {
      map: null,
      callMarkersLayer: null,
      addingMarkersLayer: null,
      buildingMarkersLayer: null,
      addBuilding: false,
      formData: {
        name: "",
        address: "",
        coordinates_latitude: 0,
        coordinates_longitude: 0,
        type_id: 1,
      },
      typeList: null,
      selectedBuilding: null,
      selectedCall: null
    };
  },
  mounted() {
    this.map = L.map('map').setView([44.5833, -0.0333], 13);
    this.map.zoomControl.remove();

    L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
      attribution: '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors'
    }).addTo(this.map);

    this.callMarkersLayer = L.layerGroup().addTo(this.map);
    this.addingMarkersLayer = L.layerGroup().addTo(this.map);
    this.buildingMarkersLayer = L.layerGroup().addTo(this.map);

    this.getBuildings();
    this.getCalls();
  },
  methods: {
    getCalls() {
      axios.get('http://127.0.0.1:8000/api/calls')
          .then(response => {
            const calls = response.data;
            calls.forEach(call => {
              const marker = L.marker([call.coordinates_latitude, call.coordinates_longitude]);
              this.callMarkersLayer.addLayer(marker);
              console.log("ok");
              marker.on('click', function(e) {
                this.selectedCall = call;
                console.log(this.selectedCall);
              }.bind(this));
            });
          })
          .catch(error => {
            console.error('Error:', error);
          });
    },
    getBuildings() {
      axios.get('http://127.0.0.1:8000/api/buildings')
          .then(response => {
            const buildings = response.data;
            buildings.forEach(building => {
              const marker = L.marker([building.coordinates_latitude, building.coordinates_longitude]);
              this.buildingMarkersLayer.addLayer(marker);
              marker.on('click', function(e) {
                this.selectedBuilding = building;
                console.log(this.selectedBuilding);
              }.bind(this));
            });
          })
          .catch(error => {
            console.error('Error:', error);
          });
    },
    toggleAddBuilding(){
      if (!this.addBuilding) {
        axios.get('http://127.0.0.1:8000/api/building-types')
            .then(response => {
              this.typeList = response.data;
              this.addBuilding = !this.addBuilding;

              const center = this.map.getCenter();
              this.formData.coordinates_latitude = center.lat.toFixed(8);
              this.formData.coordinates_longitude = center.lng.toFixed(8);
              const marker = L.marker([this.formData.coordinates_latitude, this.formData.coordinates_longitude], { draggable: true }).addTo(this.map);
              this.addingMarkersLayer.addLayer(marker);
              marker.on('dragend', (event) => {
                const markerLatLng = marker.getLatLng();
                this.formData.coordinates_latitude = markerLatLng.lat.toFixed(8);
                this.formData.coordinates_longitude = markerLatLng.lng.toFixed(8);

                axios.get(`https://nominatim.openstreetmap.org/reverse?lat=${this.formData.coordinates_latitude}&lon=${this.formData.coordinates_longitude}&format=json`)
                    .then((response) => {
                      const data = response.data;
                      this.formData.address = data.display_name || 'Adresse non trouvée';
                    })
                    .catch((error) => {
                      console.error('Erreur lors de la requête Axios :', error);
                    });
              });
            })
            .catch(error => {
              console.error('Error:', error);
            });
      }else{
        this.typeList = null;
        this.addBuilding = !this.addBuilding;
        this.addingMarkersLayer.clearLayers();
        this.formData= {
          name: "",
            address: "",
            coordinates_latitude: 0,
            coordinates_longitude: 0,
            type_id: 1,
        };
      }

    },
    submitForm() {
      axios
          .post('http://127.0.0.1:8000/api/buildings', this.formData)
          .then((response) => {
            const building = response.data;
            console.log(building)
            console.log("test")
            const marker = L.marker([building.coordinates_latitude, building.coordinates_longitude]);
            this.buildingMarkersLayer.addLayer(marker);
            this.selectedBuilding = building;

            this.typeList = null;
            this.addBuilding = !this.addBuilding;
            this.addingMarkersLayer.clearLayers();
          })
          .catch((error) => {
            console.error('Erreur lors de la requête :', error);
          });
    },
    closeBuildingInfos(){
      this.selectedBuilding = null;
    },
    closeCallInfos(){
      this.selectedCall = null;
    },
    createCall(){
      const call = {
        "coordinates_latitude": parseFloat((Math.random() * (44.58224 - 44.58193) + 44.58193).toFixed(5)),
        "coordinates_longitude": parseFloat((Math.random() * (-0.03638 - -0.03603) + -0.03603).toFixed(5)),
        "datetime": "2023-09-25T10:04:32.954Z",
        "completion_datetime": "2023-09-25T10:04:32.954Z",
        "scenario_id": 1,
        "mission_status": "En cours"
      }

      axios
          .post('http://127.0.0.1:8000/api/calls', call)
          .then((response) => {
            const call = response.data;
            const marker = L.marker([call.coordinates_latitude, call.coordinates_longitude]);
            this.callMarkersLayer.addLayer(marker);

          })
          .catch((error) => {
            console.error('Erreur lors de la requête :', error);
          });
    }
  }
};
</script>

<style>
.map-controls {
  position: absolute;
  bottom: 20px;
  right: 20px;
  z-index: 1000;
}

.map-controls button{
  border: none;
  outline: none;
  cursor: pointer;
  width: 65px;
  height: 65px;
  background-color: #88282A;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
}

.map-controls button:hover{
  background-color: #651f20;
}


#page {
  display: flex;
}

#actions {
  flex: 1;
}

#content {
  flex: 1;
}

#map {
  width: 100%;
  height: 90vh;
}

</style>