<template>

  <!--Div para contener el titulo h3 con nombre de clase 'title-PACS'
  <div class="title-PACS">
    <h3>Sistema Computarizado para el Archivado Digital de Imágenes Médicas (PACS)</h3>
  </div>-->

  <div id="map-container">
    <div class="map-wrapper">
      <svg
        id="mapa-peru"
        xmlns="http://www.w3.org/2000/svg"
        viewBox="-20 0 600 800" 
        preserveAspectRatio="xMidYMid meet"
        @mousemove="updateTooltipPosition"
        @mouseleave="hideTooltip"
      > <!-- Cambios en ViewBox: origen eje x, origen eje y, ancho, alto -->
        <!-- Dibujar las provincias desde el JSON -->
        <path
          v-for="province in provinces"
          :key="province['@id']"
          :d="province['@d']"
          :title="province['@name']"
          @mouseover="handleHover(province)"
          @mouseleave="resetHover"
          @click="handleClick(province)"  
          :style="{ fill: hoveredProvince === province['@id'] ? '#87CEEB' : '#d3d3d3', stroke: '#28586c', strokeWidth: 1 }"
        />
      </svg>

      <!-- Tooltip para mostrar información de la provincia -->
      <div
        v-if="tooltipVisible"
        :style="tooltipStyles"
        class="tooltip"
      >
        <strong>{{ tooltipData.name }}</strong><br />
        <!--ID: {{ tooltipData.id }}-->
      </div>
    </div>

    <!-- Lista de PACs dinámica -->
    <div class="pac-list">
      <h3>PACS en {{ selectedRegion ? selectedRegion : 'Perú' }}</h3>
      <ul>
        <li
          v-for="pac in pacsToShow"
          :key="pac.id"
          @click="redirectTo(pac.url)"  
          style="cursor: pointer;"
        >
          {{ pac.name }}
        </li>
      </ul>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue';

import provincesData from '../assets/peru-provinces-converted.json';

// Variables reactivas
const provinces = ref(provincesData); // Datos de las provincias
const hoveredProvince = ref(null); // Provincia sobre la que se hace hover
const tooltipVisible = ref(false); // Mostrar u ocultar el tooltip
const tooltipData = ref({ name: '', id: '' }); // Datos mostrados en el tooltip

const tooltipStyles = ref({
  position: 'absolute',
  top: '0px',
  left: '0px',
  backgroundColor: '#fff',
  border: '1px solid #ccc',
  padding: '5px',
  borderRadius: '5px',
  pointerEvents: 'none',
  zIndex: 10,
});

const selectedRegion = ref(null); // Región seleccionada

const pacs = ref({
        'Loreto': [{ id: 1, name: 'H.I. Yurimaguas', url: 'http://172.26.28.113/kWebViewer/main.jsp' }],
        'Tumbes': [{ id: 2, name: 'PACs Tumbes', url: 'http://172.26.28.113/kWebViewer/main.jsp' }],
        'Piura': [],
        'Lambayeque': [{ id: 4, name: 'PACs Almanzor Aguinaga', url: 'http://172.27.96.110:8080/kWebViewer/' }],

        'La Libertad': [{ id: 5, name: 'PAC H. Virgen de la Puerta', url: 'http://172.27.71.6:8080/kWebViewer/main.jsp' },
                        { id: 6, name: 'PAC H. Victor Lazarte Echegaray', url: 'http://172.26.141.57:8080/kWebViewer/main.jsp' },
                        { id: 7, name: 'PAC H. Virú', url: 'http://172.27.40.82:8080/kWebViewer/main.jsp' },
                        { id: 8, name: 'PAC H. Moche', url: 'http://172.30.145.15:8080/kWebViewer/main.jsp' },
                        { id: 9, name: 'PAC H. La Esperanza', url: 'http://172.30.149.149:8080/kWebViewer/main.jsp' },
                        { id: 10, name: 'PAC H. Florencia de Mora', url: 'http://172.30.147.93:8080/kWebViewer/main.jsp' },
                        { id: 11, name: 'PAC H. Albretch', url: 'http://172.30.146.7:8080/kWebViewer/main.jsp' },
                        { id: 12, name: 'PAC H. Casa Grande', url: '' },
                        { id: 13, name: 'PAC Policlínico El Porvenir', url: 'http://172.30.148.14/kWebViewer/main.jsp' },
                        { id: 14, name: 'PAC H. Victor Larco', url: '' },
                        { id: 15, name: 'PAC H. Pacasmayo', url: 'http://172.30.110.75:8080/kWebViewer/main.jsp' },
        ],

        'Ancash': [{ id: 16, name: 'PACs Chimbote', url: 'http://172.26.53.246:8080/kWebViewer/' }],

        'Lima': [{ id: 17, name: 'PACs Almenara', url: 'http://172.22.17.9:8080/kWebViewer/' },
                  { id: 18, name: 'PACs Rebagliati', url: 'http://172.25.42.5:8080/kWebViewer/main.jsp' },
                  { id: 19, name: 'PACs NACIONAL', url: 'http://10.15.4.30:8080/rall3old/172.26.147.24/kWebViewer' },
                  { id: 20, name: 'PACs Sabogal', url: 'http://172.22.55.100:8080/kWebViewer/' },
                  { id: 21, name: 'PACs Mongrut', url: 'http://172.24.27.234/kWebViewer/main.jsp?lang=es' },
                  { id: 22, name: 'PACs INCOR', url: 'http://172.25.10.179:8080/kWebViewer/main.jsp' }
        ],

        'Ica': [],
        'Arequipa': [],
        'Moquegua': [{ id: 25, name: 'PACs Moquegua', url: 'http://172.26.70.120:8080/kWebViewer/' }],
        'Cajamarca': [{ id: 26, name: 'PACs H. II Cajamarca', url: 'http://172.26.57.115:8080/kWebViewer/main.jsp' }],
        'Huánuco': [],
        'Pasco': [],
        'Junín': [],
        'Huancavelica': [],
        'Ayacucho': [],
        'Apurímac': [{ id: 32, name: 'PAC H. Abancay', url: 'http://172.27.84.22/kWebViewer/main.jsp' },
                    { id: 33, name: 'PAC Hosp. I Andahuaylas', url: 'http://172.30.11.11//kWebViewer/main.jsp' },
        ],
        'Cusco': [{ id: 34, name: 'PACs Cusco', url: 'http://172.26.18.5:8080/kWebViewer/main.jsp' }],
        'Puno': [],
        'Amazonas': [],
        'San Martín': [{ id: 37, name: 'H. II Tarapoto', url: 'http://172.27.83.17:8080/kWebViewer/main.jsp' },
                      { id: 38, name: 'H. I Juanji', url: 'http://172.30.210.95:8080/kWebViewer/main.jsp' },
        ],
        'Ucayali': [],
        'Madre de Dios': [],
        'Tacna': [],
        /*
        'Default': [
          { id: 1, name: 'PAC H. Virgen de La Puerta' },
          { id: 2, name: 'PAC H. Victor Lazarte Echegaray' },
          { id: 3, name: 'PAC H. Virú' },
          { id: 4, name: 'PAC H. Moche' }
        ]*/
});

// Computed property para mostrar la lista de PACs según la región seleccionada
const pacsToShow = computed(() => {
  return selectedRegion.value && pacs.value[selectedRegion.value]
    ? pacs.value[selectedRegion.value]
    : pacs.value['Default'];
});

// Métodos
const handleHover = (province) => {
  hoveredProvince.value = province['@id']; // Cambiar color al hacer hover
  tooltipData.value = { name: province['@name'], id: province['@id'] }; // Mostrar tooltip
  tooltipVisible.value = true;
};

const resetHover = () => {
  hoveredProvince.value = null; // Resetear color al salir del hover
  tooltipVisible.value = false; // Ocultar tooltip
};

const handleClick = (province) => {
  selectedRegion.value = province['@name']; // Cambiar la región seleccionada
};

const updateTooltipPosition = (event) => {
  // Actualizar posición del tooltip
  tooltipStyles.value.top = `${event.pageY + 10}px`;
  tooltipStyles.value.left = `${event.pageX + 10}px`;
};

const hideTooltip = () => {
  tooltipVisible.value = false; // Ocultar el tooltip si el mouse sale del SVG
};

const redirectTo = (url) => {
  window.open(url, '_blank'); // Abrir el enlace en una nueva pestaña
};
</script>


<style scoped>

.title-PACS {
  width: 50%;  /* Tamaño del mapa */
  padding: 20px;
  border: 1px solid #ccc;
  box-shadow: 0 4px 8px rgba(23, 238, 4, 0.1);
  background-color: rgb(255, 255, 255);

  border-radius: 5px;
  font-family: Arial;
}

/*abarca al mapa y al contenedor de lista de PACS*/
#map-container {
  display: flex;
  justify-content: center;
  gap: 50px; /* Espacio entre el mapa y la lista */
  align-items: flex-start;
  height: 75vh;

  overflow: visible; /* Asegura que el tooltip sea visible */
  position: relative; /* Necesario para posicionar correctamente elementos hijos absolutos */
  
}

.map-wrapper {
  width: calc(30% - 45px);  /* Ajusta espacio extra estaba en 70 o 60*/
  padding: 5px;
  border: 1px solid #ccc;
  box-shadow: 0 4px 8px rgba(23, 238, 4, 0.1);
  border-radius: 10px; /* bordes */
  background-color: rgb(255, 255, 255); /* color de fondo */
  position: flex;/* posicion de las letras */ 
  /*display: flex;*/
  font-family: Arial;

}

.pac-list {
  width: 30%;  /* Tamaño de la lista de PACs */
  padding: 20px;
  background-color: white;
  border: 1px solid #ccc;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  border-radius: 10px;
  font-family: Arial;
}

ul {
  list-style: none;
  padding: 0;
}

li {
  padding: 10px;
  margin: 5px 0;
  background-color: #f9f9f9;
  cursor: pointer;
}

/*Fondo de sombreado de listado de PACS*/ 
li:hover {
  background-color: #87CEEB;
}

svg {
  width: 100%;  /* Escalar el SVG */
  height: auto;
}

path:hover {
  cursor: pointer;
}

.tooltip {
  background-color: white;
  border: 1px solid gray;
  padding: 5px;
  position: absolute; /* Para permitir posicionamiento dinámico */
  pointer-events: none; /* Evita interferencias en eventos */
  z-index: 9999; /* Asegura que esté encima de todo */
  border-radius: 4px; /* Bordes redondeados */
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2); /* Agrega un efecto de sombra */
}

</style>
