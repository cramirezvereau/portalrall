<script setup>

import MapaPeru from './components/MapaPeru.vue'; //componente que contiene el Mapa del Peru para PACS
import AnexosLista from './components/AnexosLista.vue';

import HeaderRall from './components/HeaderRall.vue'; //componente que contiene la cabecera llamada 'Header'

import PapeletaRall from './components/PapeletaRall.vue';


//Utilidades reactivas y hook
import { ref, onMounted, onBeforeUnmount } from 'vue';

const isMenuOpen = ref(false)  // Estado del menú abierto/cerrado.
const recentLinks = ref([]); // para almacenar el arreglo de links
const showPopup = ref(false); // Indica si el popup está visible.
const popupUrl = ref(''); // URL que se mostrará en el popup.
const navegador = ref(''); // Variable para almacenar el navegador detectado

// Definimos variables reactivas para el ancho y alto de la pantalla
const screenWidth = ref(window.innerWidth);
const screenHeight = ref(window.innerHeight);

// Función para actualizar el tamaño de pantalla
const updateScreenSize = () => {
  screenWidth.value = window.innerWidth;
  screenHeight.value = window.innerHeight;
};

//Abre un popup con la URL especificada.
function openPopup(url) {
  popupUrl.value = url;
  showPopup.value = true;
}

// Cierra el popup y limpia la URL.
function closePopup() {
  showPopup.value = false;
  popupUrl.value = '';
}

//funcion para el guardado de links del contenedor de reciente
function addRecentLink(link) {

  const exists = recentLinks.value.some((item) => item.url === link.url);
  
  if (!exists) {
    recentLinks.value.push(link);

    if (recentLinks.value.length > 6) {
      recentLinks.value.shift(); // Elimina el más antiguo si hay más de 6.
    }

    localStorage.setItem('selectedLinks', JSON.stringify(recentLinks.value));
  }
}

// Detección de navegador web
function detectarNavegador() {
  const userAgent = navigator.userAgent;

  if (userAgent.includes("Edg/")) {
    navegador.value = "Microsoft Edge";
  } else if (userAgent.includes("OPR") || userAgent.includes("Opera")) {
    navegador.value = "Opera";
  } else if (userAgent.includes("Chrome")) {
    navegador.value = "Google Chrome";
  } else if (userAgent.includes("Firefox")) {
    navegador.value = "Mozilla Firefox";
  } else if (userAgent.includes("Safari")) {
    navegador.value = "Safari";
  } else {
    navegador.value = "Navegador desconocido";
  }
}

onMounted(() => {
  const savedLinks = JSON.parse(localStorage.getItem('selectedLinks'));
  if (savedLinks) {
    recentLinks.value = savedLinks;
  }

  detectarNavegador(); // Detecta el navegador al cargar el componente.
  updateScreenSize(); // Actualiza el tamaño de la pantalla.
  window.addEventListener("resize", updateScreenSize); // Escucha cambios de tamaño.
});

  // Remover el listener cuando el componente se destruya
  onBeforeUnmount(() => {
  window.removeEventListener("resize", updateScreenSize); // Limpia el evento al desmontar.
  });
</script>

<template>

  <!-- div para almacenar el mensaje de bloque  de navegador-->
  <div v-if="navegador !== 'Microsoft Edge'" class="overlay">
    <div class="popup">   <!-- Clase para el mensaje de navegador, en este caso no se muestra el mensaje pero ya esta condicionado-->
      <p> </p>
    </div>
  </div>

  <!-- Iniciamos el contenido del menu principal-->
  <div class="header-container">

    <HeaderRall @link-clicked="addRecentLink" ></HeaderRall>
    
    <div class="dividerup"> </div> 

    <main>
          
          <div class="all">  
            
            <!--contenedor de la imagen de la imagen 'ESSALUD'-->
            <div class="logo-title-container"> <!-- clase del logo del centro de la pantalla -->
            
              <img src="./assets/icons/logo.png" alt="Logo" class="logo-main">
              <h1 class="main-title"> Red Asistencial La Libertad </h1>
              
            </div>
          
          <!-- contenedor de opciones que se escogen segun se abran-->
          <div class="card-container">

            <a v-for="(link, index) in recentLinks.slice(0, 6)" 
              :key="index" 
              class="card" 
              :href="link.url" 
              target="_blank" >
                <h2>{{ link.title }} </h2> <!-- Links almacenados en el array de 'recentlinks', 
                parte inferior en el script . Asignado a savedLinks -> SelectedLinks  -->
                <img class="logo-title-container-card" src="./assets/icons/logo.png" alt="Imagen">
            </a>
          
          </div>

          <!--Clase que abarca los mensajes de Navegador y Resolucion de Pantalla-->
          <div class="msg-browser">
            <p>Estás utilizando: {{ navegador }}</p>
            <p>Resolución actual: {{ screenWidth }} x {{ screenHeight }}</p>
          </div>

        </div>
        
          <!--<div class="image-container">
            <a href="#" @click="openPopup('http://10.15.4.30:8080/rall3old/#/chat')">
              <img src="./assets/asistente.png" alt="Imagen">
            </a>
          </div>
          <div class="popup" ref="popup" v-if="showPopup">
            <div class="popup-content">
              <iframe :src="popupUrl"></iframe>
              <button @click="closePopup">Cerrar</button>
            </div>
          </div>-->
          
          <!--Clase que separa la parte media de la pantalla, color : #264EB5 (SOLO VALIDO EN RESOLUCIONES DE PANTALLA MENORES 2500px x 1300px EN ADELANTE)
            PARA PANTALLAS DE 2500px x 1300px EN ADELANTE, el color es: #ffffff  -                                                                                                                                        -->
          <div class="dividermid"> 
            <!--No contiene nada, solo sirve como un espaciador de color entero-->
          </div>

          <!-- Div que contiene la imagen de ayuda de la chica 
          <div class="image-container">
            <a>
              <img src="./assets/icons/asistente.png" alt="Imagen">
            </a>
          </div>-->

      </main>
  
  </div> <!-- cierrre de la clase 'all' -->

  <!--VISTA DE PACS -->
  <div class="offcanvas offcanvas-start full-screen-offcanvas" 
      data-bs-backdrop="static" 
      tabindex="-1" id="MapaPeru-dropdown" 
      aria-labelledby="staticBackdropLabel">

      <div class="offcanvas-header">
              
        <h3 class="offcanvas-title" id="staticBackdropLabel">Sistema Computarizado para el Archivado Digital de Imágenes Médicas (PACS)</h3>
          <button type="button" 
                  class="btn-close" 
                  data-bs-dismiss="offcanvas" 
                  aria-label="Close">
          </button>

          </div>
    
            <div class="contentview">
              <div class="offcanvas-body">
                <div><!--
                    This offcanvas now occupies the entire screen.-->
                    <MapaPeru> </MapaPeru>
                </div>
              </div>
            </div>
  </div>

    <!--VISTA DE ANEXOS HOSPITALES -->
    <div class="offcanvas offcanvas-start full-screen-offcanvas" 
      data-bs-backdrop="static" 
      tabindex="-1" id="AnexosLista-dropdown" 
      aria-labelledby="staticBackdropLabel">

      <div class="offcanvas-header">
              
        <h3 class="offcanvas-title" id="staticBackdropLabel">Lita de anexos hospitales ESSALUD La Libertad</h3>
          <button type="button" 
                  class="btn-close" 
                  data-bs-dismiss="offcanvas" 
                  aria-label="Close">
          </button>

          </div>
    
            <div class="contentview">
              <div class="offcanvas-body">
                <div><!--
                    This offcanvas now occupies the entire screen.-->
                    <AnexosLista> </AnexosLista>
                </div>
              </div>
            </div>
  </div>

  <!--Prueba para el FORMATO DE PAPELETAS-->
  <div class="offcanvas offcanvas-start full-screen-offcanvas" 
      data-bs-backdrop="static" 
      tabindex="-1" id="staticBackdrop2" 
      aria-labelledby="staticBackdropLabel">

      <div class="offcanvas-header">
              
        <h3 class="offcanvas-title" id="staticBackdropLabel">Formato de Papeletas</h3>
          <button type="button" 
                  class="btn-close" 
                  data-bs-dismiss="offcanvas" 
                  aria-label="Close">
          </button>

          </div>
    
            <div class="contentviewPAPELETAS">
              <div class="offcanvas-body">
                <div><!--
                    This offcanvas now occupies the entire screen.-->
                    <PapeletaRall> </PapeletaRall>
                </div>
              </div>
            </div>
  </div>

</template>

<style scoped>

/* Clase que abarca todo el contenido de la pantalla  */
.all {
    display: flex; /* Usa Flexbox para centrar el contenido */
    flex-direction: column; /* Asegura que los elementos se apilen verticalmente */
    align-items: center; /* Centra horizontalmente */
    justify-content: center; /* Centra verticalmente */
    width: 100%; /* Abarca todo el ancho de la pantalla */
    height: 100vh; /* Abarca toda la altura de la pantalla */
    margin: 0; /* Elimina márgenes */
    padding: 0;  /*Elimina padding */
    box-sizing: border-box; /*Asegura que el padding esté incluido en el ancho y alto */
    background-color: #ffffff; /* Color de fondo (ajusta según tus necesidades) */
}

/*estilos full screen del modal de bootstrap para el mapa del peru*/ 
.full-screen-offcanvas {
  width: 100% !important; /* Ocupa todo el ancho */
  height: 100% !important; /* Ocupa toda la altura */
  top: 0; /* Asegura que comience desde la parte superior */
  left: 0; /* Asegura que comience desde la izquierda */
  position: fixed; /* Fija el panel en la pantalla */
  z-index: 1055; /* Asegura que esté por encima de otros elementos */
  overflow-y: auto; /* Permite desplazamiento vertical si el contenido es grande */
}

/**/ 
body {
    background-color: #ffffff;
}

/* propiedades de la linea inferior azul */
.dividermid {
  position: fixed; /* Fija el elemento en un lugar específico */
  bottom: 0; /* Lo coloca en la parte inferior de la ventana */
  left: 0; /* Asegura que se alinee al lado izquierdo */
  width: 100%; /* Asegura que ocupe todo el ancho de la pantalla */
  background-color: #264EB5; /* Color de fondo */
  padding: 2rem; /* Ajusta el relleno según sea necesario */
  box-sizing: border-box; /* Incluye el padding dentro del ancho total */
  /*z-index: 1000;  Lo asegura por encima de otros elementos */
  }

/* Propiedades del contenedor del titulo */
.logo-title-container {
  display: flex;
  flex-direction: column; /* Asegura que los elementos estén en columna */
  align-items: center; /* Centra horizontalmente */
  justify-content: center; /* Centra verticalmente */
  padding: 2.4rem; /*borde blando alrededor de  la imagen*/
  background-color: #ffffff;
  max-width: 100%;
   /*height: 100vh; Asegura que ocupe toda la altura visible */
}

/* Asegura que los elementos estén en columna */
.logo-main {
    width: 450px;
}

/* Propiedades del titulo principal */
.main-title {
    font-size: 30px;
    font-weight: 1;
    margin: 0;
    color: #0076c7;
    font-family: Arial;
}

/* clase del contenedor de la cartilla grande que abarca las pequeñas */
.card-container {
  width: 80%;
  max-width: 900px;
  display: flex;
  flex-wrap: wrap; /* Permite que las tarjetas salten a la siguiente fila */
  justify-content: center; /* Centra horizontalmente las tarjetas */
  gap: 20px; /* Ajusta el espacio entre las tarjetas */
  margin: 0 auto; /* Centra el contenedor horizontalmente */
  padding: 0.5rem;
}

/* Clase de las cartillas pequeñas */
.card {
  flex: 1 1 calc(33.33% - 20px); /* Ajusta el ancho de cada tarjeta */
  max-width: 300px; /* Limita el ancho máximo */
  padding: 10px;
  border: 1px solid #ddd;
  border-radius: 10px;
  box-sizing: border-box;
  text-decoration: none; /* Remueve el subrayado */
  color: inherit; /* Mantiene el color del texto */
  display: flex;
  flex-direction: column; /* Coloca los elementos de la tarjeta en columna */
  align-items: center; /* Centra horizontalmente el contenido */
  justify-content: center; /* Centra verticalmente el contenido */
}
.card h2 {
  color: #0076c7;
  font-family: Arial;
  font-weight: 1;
  text-align: center;
  text-decoration: none; /* Remueve el subrayado */
}
.card img {
  width: auto;
  height: 60px; /* Tamaño fijo para las imágenes */
  margin-bottom: 10px; /* Espacio entre la imagen y el título */
  display: block;
}

/* Clase de la version anterior de rall*/
.popup {
  position: fixed;
  top: 0;
  right: 0;
  bottom: 0;
  left: 0;
  background: rgba(0, 0, 0, 0.5);
  display: flex;
  align-items: center;
  justify-content: flex-end;
  z-index: 2;
}
.popup-content {
  background-color: #fff;
  padding: 0 0 50px;
  overflow: hidden;
  box-shadow: 0 0 10px rgba(2, 229, 245, 0.2);
  width: 27vw;
  height: 30vw;
}
iframe {
  width: 100%;
  height: 100%;
  border: none;
}
button{
  background-color: red;
  color: white;
}

/* Asegura que los elementos estén en columna */
.image-container {
  position: fixed;
  bottom: 0;
  right: 0;
  margin: 20px;
  z-index: 1;
}
.image-container img {
  max-width: 250px;
  margin-right: -5px;    
}

/* estilo del contenedor de del mapa*/
.contentview {
  display: flex;
  justify-content: flex;
  align-items: flex;
  flex-grow: 1;
  width: flex;
  max-width: flex;
  padding:10px;
  background-color: #87CEEB;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
}

.contentviewPAPELETAS {
  display: flex;
  justify-content: flex;
  align-items: flex;
  flex-grow: 1;
  width: flex;
  max-width: flex;
  padding:10px;
  background-color: #87CEEB;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
}

/* Propiedades para el pop up de bloqueo de mensaje*/
.popup {
  background-color: white;
  padding: 20px;
  border-radius: 10px;
  text-align: center;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.3);
}
.popup p {
  font-size: 18px;
  color: #333;
}

</style>
