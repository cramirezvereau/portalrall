<template>
    <div class="container-fluid anexos-container mt-4 full-height">
      <div class="row transition">
        <!-- Lista de hospitales -->
        <div :class="selectedHospital ? 'col hospital-list slide-left' : 'col-md-12 hospital-list'">
          <h3>Hospitales en La Libertad</h3>
          <ul class="list-group">
            <li
              v-for="hospital in hospitales"
              :key="hospital.id"
              class="list-group-item list-group-item-action"
              :class="{ 'active-hospital': hospital === selectedHospital }"
              @click="selectHospital(hospital)"
            >
              {{ hospital.nombre }}
            </li>
          </ul>
        </div>
        
        <!-- Lista de anexos -->
        <div class="col text-center" v-if="selectedHospital">
          <h3>Anexos de {{ selectedHospital.nombre }}</h3>
          <ul class="list-group">
            <li v-for="anexo in selectedHospital.anexos" :key="anexo.numero" class="list-group-item">
              {{ anexo.numero }} - {{ anexo.descripcion }}
            </li>
          </ul>
          <button class="btn btn-secondary mt-3" @click="selectedHospital = null">Volver</button>
        </div>
      </div>
    </div>
  </template>
  
  <script>
  import data from "@/assets/data.json"; // Asegúrate de tener el JSON en assets
  
  export default {
    data() {
      return {
        hospitales: [],
        selectedHospital: null,
      };
    },
    created() {
      this.hospitales = data.hospitales;
    },
    methods: {
      selectHospital(hospital) {
        this.selectedHospital = hospital;
      },
    },
  };
  </script>
  
  <style scoped>

  .anexos-container {
    display: flex;
    justify-content: center;
    align-items: center;
  }
  
  .slide-left {
    transform: translateX(-25%);
    transition: transform 0.5s ease-in-out;
  }
  
  
  .hospital-list {
    width: 100%;
    padding: 0 10%;
  }
  
  .active-hospital {
    font-weight: bold;
    background-color: rgba(255, 255, 255, 0.2);
  }
  
  .anexos-container h3 {
    margin-bottom: 20px;
  }
  </style>
  