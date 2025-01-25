<script setup>
    import { ref, onMounted, onUnmounted, defineEmits  } from 'vue'; 

    import { useRouter } from 'vue-router'; //uso en tooglePACs

    const navegador = ref(''); // Variable para almacenar el navegador detectado

    const router = useRouter(); // Inicializamos Vue Router , para el manejo de rutas usado en PACS

// Estado del menú desplegable
    const recentLinks = ref([]); // Links recientes
    const isMenuOpen = ref(false); // Controla si el menú principal está abierto
    const isFormatosOpen = ref(false); // Controla si el menú "Formatos" está abierto
    const isTransparenciaOpen = ref(false); // Controla si el menú "Transparencia" está abierto
    const isPACsOpen = ref(false); // Controla si el menú "PACs" está abierto

//para contadores de tiempo
    let menuTimer = null; 
    let formatosTimer = null; 
    let transparenciaTimer = null; 
    let PACsTimer = null;

// Emitir eventos
    const emit = defineEmits(['link-clicked']); //Define un evento llamado link-clicked

    const linkClicked = (title, url) => { 
    emit('link-clicked', { title, url }); //Se emite el evento cuando se selecciona un link, enviando su título y URL.
    isMenuOpen.value = false;
    isFormatosOpen.value = false;
    isTransparenciaOpen.value = false;
    isPACsOpen.value = false;
    };

// Función para alternar el menú
//función para alternar su estado (abrir/cerrar) y gestionar los temporizadores.
    const toggleMenu = (event) => {
    event.stopPropagation(); // Detener propagación para evitar cerrar inmediatamente
    isMenuOpen.value = !isMenuOpen.value;
    resetTimer();//para timer

    if (isMenuOpen.value) {
        startTimer();//para timer
        
        isFormatosOpen.value = false;
        isTransparenciaOpen.value = false;
        isPACsOpen.value = false;
    }else {
        clearTimeout(menuTimer);
    }
    };

// Función para alternar el formatos
    const toggleFormatos = (event) => {
    event.stopPropagation(); // Detener propagación para evitar cerrar inmediatamente
    isFormatosOpen.value = !isFormatosOpen.value;
    resetTimer();//para timer

    if (isFormatosOpen.value) {
        startTimer();//para timer

        isMenuOpen.value = false;
        isTransparenciaOpen.value = false;
        isPACsOpen.value = false;
    }else {
        clearTimeout(menuTimer);
    }
    };

    // Función para alternar el transparencia
    const toggleTransparencia = (event) => {
    event.stopPropagation(); // Detener propagación para evitar cerrar inmediatamente
    isTransparenciaOpen.value = !isTransparenciaOpen.value;
    resetTimer();//para timer

    if (isTransparenciaOpen.value) {
        startTimer();//para timer

        isMenuOpen.value = false;
        isFormatosOpen.value = false;
        isPACsOpen.value = false;
    }else {
        clearTimeout(menuTimer);
    }
    };

    // Función para alternar el PACS
    const togglePACs = () => {
    isPACsOpen.value = !isPACsOpen.value;

    };

//Funcion para detectar el Navegador en uso
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

//para timer
    function startTimer() { //Cierra automáticamente los menús después de 15 segundos si no hay interacción.
    clearTimeout(menuTimer, formatosTimer, transparenciaTimer, PACsTimer); 

    menuTimer = setTimeout(() => {
        isMenuOpen.value = false;
    }, 15000); //10 segundos

    formatosTimer = setTimeout(() => { // cambio
        isFormatosOpen.value = false;
    }, 15000); //10 segundos

    transparenciaTimer = setTimeout(() => { // cambio
        isTransparenciaOpen.value = false;
    }, 15000); //10 segundos

    PACsTimer = setTimeout(() => { // cambio
        isPACsOpen.value = false;
    }, 15000); //10 segundos
    };

//para timer
    function resetTimer() { //Limpia el temporizador actual y lo reinicia si un menú está abierto.
    if (isMenuOpen.value) {
        clearTimeout(menuTimer);
        startTimer();
    } else if (isFormatosOpen.value) {
        clearTimeout(formatosTimer);
        startTimer();
    } else if (isTransparenciaOpen.value) {
        clearTimeout(transparenciaTimer);
        startTimer();
    } else if (isPACsOpen.value) {
        clearTimeout(PACsTimer);
        startTimer();
    }
    };

//Cerrar menús al hacer clic fuera
    const handleClickOutside = (event) => {
    const menuElement = document.querySelector('.menu-dropdown');
    const formatosElement = document.querySelector('.formatos-dropdown');
    const transparenciaElement = document.querySelector('.transparencia-dropdown');
    const PACsElement = document.querySelector('.pacs-dropdown');

    // Evitar cerrar si el clic ocurrió dentro de los menús
    if (
        (menuElement && menuElement.contains(event.target)) ||
        (formatosElement && formatosElement.contains(event.target)) ||
        (transparenciaElement && transparenciaElement.contains(event.target))||
        (PACsElement && PACsElement.contains(event.target))
    ) {
        return;
    }

// Cerrar todos los menús si el clic ocurrió fuera
    isMenuOpen.value = false;
    isFormatosOpen.value = false;
    isTransparenciaOpen.value = false;
    isPACsOpen.value = false;
    };

// Opcional: Cambiar el cursor
    const cambioCursor = (cursor) => {
    document.body.style.cursor = cursor;
    };

//Para los eventos de deectar navegador y 'handleClickOutside'
    onMounted(() => {
    
        detectarNavegador();
        //document.addEventListener('mousedown', handleClickOutside);
        document.addEventListener('click', handleClickOutside);
    });
    
//Para los eventos de deectar navegador y 'handleClickOutside'
    onUnmounted(() => {
        //document.removeEventListener('mousedown', handleClickOutside);
        document.removeEventListener('click', handleClickOutside);
        clearTimeout(menuTimer);//para timer
    });

</script>

<template>

<header class="overlay" @mousedown="resetTimer"> 
    
<!-- Contenedor principal para que sea responsivo-->
<div class="container-fluid">

    <div class="header-content">

            <!-- div para almacenar el mensaje de bloque  de navegador-->
            <!-- Mensaje de error del uso de Navegador -->
            <!-- Mensaje si no es Microsoft Edge -->
            <header v-if="navegador === 'Microsoft Edge'" class="overlay">
            <!-- Contenido del menú superior si es Microsoft Edge -->
            </header>
            <div v-else class="overlay">
                <div class="popup"> <!-- Clase para el mensaje de navegador, en este caso no se muestra el mensaje pero ya esta condicionado-->
                <p>Esta aplicación solo funciona en Microsoft Edge. Estás usando: {{ navegador }}</p>
                </div>
            </div>

    <nav class="navbar navbar-expand-lg bg-body-tertiary"> <!-- Llamado para el contenedor predeterminado de bootstrap-->
    
        <div class="container-fluid">

            <!-- Logo y Título -->
            <a class="navbar-brand" href="#">

                <div class="logo-container">
                    <img src="../assets/icons/logo.png" 
                        alt="Logo" 
                        width="150" 
                        height="40" 
                        class="logo-img" >

                    <h1 class="title"> Red Asistencial La Libertad </h1>

                </div>

            </a>

            <!--Boton para el desplegable del menu principal-->
            <button 
                class="navbar-toggler" 
                type="button" 
                data-bs-toggle="collapse" 
                data-bs-target="#navbarScroll" 
                aria-controls="navbarScroll" 
                aria-expanded="false" 
                aria-label="Toggle navigation"
                >
                <span class="navbar-toggler-icon"></span>
            </button>

            <!-- INICIO DEL CONTENIDO DEL MENU SUPERIOR (Menu,Formatos,PACS,Transparencia,Convocatorias,etc) -->
            <div class="collapse navbar-collapse " id="navbarScroll">
                <!--Iniciamos el contenido de opciones que a su vez es scrolleable segun el tamaño de pantalla-->

                <!-- "ms-auto" mueve todo el contenido hacia la derecha-->
                <ul class="navbar-nav ms-auto my-2 my-lg-0 navbar-nav-scroll" style="--bs-scroll-height: 100px;">
                    
                    <!-- INICIO DE CONFIGURACION 'ICONO DE MENU DESPLEGABLE' -->
                    <li class="nav-item d-flex align-items-center"> <!-- 'd-flex align-items-center' para que las imagenes esten centradas-->
                        <img 
                            src="../assets/icons/dashboard.png" 
                            alt="Logo" 
                            width="25" 
                            height="25" 
                            class="d-inline-block align-text-top"
                            @click="toggleMenu"
                            @mouseenter="cambioCursor('pointer')"
                            @mouseleave="cambioCursor('auto')"
                            >    
                        
                    </li> <!-- FIN DE CONFIGURACION 'ICONO DE MENU DESPLEGABLE' -->
                    
                    <!-- INICIO DE CONFIGURACION 'ICONO DE MENU DESPLEGABLE' -->
                    <li class="nav-item">
                        <a class="nav-link active" 
                            aria-current="page" 
                            @click="toggleFormatos" 
                            @mouseenter="cambioCursor('pointer')" 
                            @mouseleave="cambioCursor('auto')"
                            >
                            Formatos
                        </a>
                    </li><!-- FIN DE CONFIGURACION 'ICONO DE MENU DESPLEGABLE' -->
                    
                    <!-- INICIO DE CONFIGURACION 'Transparencia' -->
                    <li class="nav-item">
                        <a class="nav-link active" 
                            aria-current="page" 
                            @click="toggleTransparencia" 
                            @mouseenter="cambioCursor('pointer')" 
                            @mouseleave="cambioCursor('auto')"
                            >
                            Transparencia
                        </a>
                    </li><!-- FIN DE CONFIGURACION 'Transparencia' -->
                    
                    <!-- INICIO DE CONFIGURACION 'PACS' 
                    <li class="nav-item">
                        <a class="nav-link active" 
                            aria-current="page" 
                            href="#">
                            PACS
                        </a>
                    </li> FIN DE CONFIGURACION 'PACS' -->

                    <li class="nav-item">
                        <a class="nav-link active" 
                            type="button" data-bs-toggle="offcanvas" 
                            data-bs-target="#staticBackdrop" 
                            aria-controls="staticBackdrop">
                            PACS
                        </a>
                    </li> 

                    <!-- INICIO DE CONFIGURACION 'Convocatorias' -->
                    <li class="nav-item">
                        <a class="nav-link active" 
                            aria-current="page" 
                            href="http://convocatorias.essalud.gob.pe/"
                            target="_blank"
                            @click="linkClicked('Convocatorias', 'http://convocatorias.essalud.gob.pe/')"
                            @mouseleave="cambioCursor('auto')">
                            Convocatorias
                        </a>
                    </li><!-- FIN DE CONFIGURACION 'Convocatorias' -->
                    
                    <!-- INICIO DE CONFIGURACION 'ICONO DE NUBE' -->
                    <li class="nav-item d-flex align-items-center">
                        <a href="https://10.15.4.25/index.php/login" 
                            target="_blank"
                            @click="linkClicked('Nube', 'http://10.15.4.25/cloud/index.php/login')" 
                            @mouseleave="cambioCursor('auto')" >
                        <img 
                            src="../assets/icons/nube.png" 
                            alt="Logo" 
                            width="25" 
                            height="25" 
                            class="d-inline-block align-text-top"
                        >
                        </a>
                    </li><!-- FIN DE CONFIGURACION 'ICONO DE NUBE' -->
                    
                    <!-- INICIO DE CONFIGURACION 'ICONO DE CARTA' -->
                    <li class="nav-item d-flex align-items-center">
                        <a href="https://outlook.live.com/mail/0/"
                            target="_blank"
                            @click="linkClicked('Mensaje', 'https://outlook.live.com/mail/0/')"
                            @mouseleave="cambioCursor('auto')">
                        <img 
                            src="../assets/icons/mensaje.png" 
                            alt="Logo" 
                            width="25" 
                            height="25" 
                            class="d-inline-block align-text-top"
                        >    
                        </a>
                    </li><!-- FIN DE CONFIGURACION 'ICONO DE CARTA' -->
                </ul>

            </div> <!-- FIN DEL CONTENIDO DEL MENU SUPERIOR  -->

        </div>
    
    </nav>
    </div>

    <!--  INICIO DE DROPDOWN DE OPCIONES DEL BOTON DE MENU  -->
    <div v-if="isMenuOpen" class="menu-dropdown navbar-nav-scroll" > <!--'navbar-nav-scroll' permite añair el scroll-->

        <!--  TITULO DE 'ASISTENCIALES'  -->
        <div class="menu-header">
            <h2 class="subtitulo">
                <img
                    src="../assets/icons/asistenciales.png"
                    alt="Icono"
                    class="icon-before-subtitulo"
                >
                Asistenciales
            </h2>
        </div> <!--  FIN DE TITULO DE 'ASISTENCIALES'  -->
        
        <!-- Botón para cerrar el menú 
        <button
            class="btn-close-menu"
            @click="toggleMenu"
            @mouseenter="cambioCursor('pointer')"
            @mouseleave="cambioCursor('auto')"
            >
            <img
                src="../assets/icons/close.png"
                alt="Cerrar menú"
                class="icon-close"
            >
        </button>-->

            <!--  INICIO DE SUB MENU 'ASISTENCIALES'  -->
            <div class="submenu ">
                <a
                    href="http://10.15.4.30:8080/rall2/atencionprimaria.html"
                    target="_blank"
                    @click="linkClicked('Atención Primaria', 'http://10.15.4.30:8080/rall2/atencionprimaria.html')"
                    class="submenu-link"
                >
                Atención Primaria
                </a>

                <a
                    href="http://oasprod.essalud.gob.pe:7777/acreditap/"
                    target="_blank"
                    @click="linkClicked('Acredita', 'http://oasprod.essalud.gob.pe:7777/acreditap/')"
                    class="submenu-link"
                >
                Acredita
                </a>

                <a
                    href="http://10.15.4.25/cloud/index.php/s/NdWNEGZFDB3ygE5"
                    target="_blank"
                    @click="linkClicked('Firma Digital', 'http://10.15.4.25/cloud/index.php/s/NdWNEGZFDB3ygE5')"
                    class="submenu-link"
                >
                Firma Digital
                </a>

                <a href="http://sgss.essalud/sgss/servlet/hmain" 
                target="_blank"
                @click="linkClicked('SGSS - ESSI', 'http://sgss.essalud/sgss/servlet/hmain')" 
                class="submenu-link">
                SGSS - ESSI
                </a>

                <!--<a href="http://172.20.0.148/ftp/malcocer/repositorio/gerencial/" target="_blank"
                @click="linkClicked('GCOP', 'http://172.20.0.148/ftp/malcocer/repositorio/gerencial/')"
                class="submenu-link">GCOP</a> -->

                <a href="http://infocal.essalud/" 
                    target="_blank" 
                    @click="linkClicked('Infocal', 'http://infocal.essalud/')"
                    class="submenu-link">
                    Infocal
                </a>

                <a href="http://sigi.essalud:8080/cittcontrol/" 
                target="_blank"
                @click="linkClicked('SIGI', 'http://sigi.essalud:8080/cittcontrol/')" 
                class="submenu-link">
                SIGI
                </a>

                <a href="http://anatpat.essalud/ANATPAT/" 
                target="_blank"
                @click="linkClicked('Acredita')" 
                class="submenu-link">
                Anapat
                </a>

                <a href="http://10.0.0.218/" 
                target="_blank"
                @click="linkClicked('Anapat', 'http://oasprod.essalud.gob.pe:7777/acreditap/')" 
                class="submenu-link">
                Violencia Familiar
                </a>

                <a href="http://10.15.4.25/cloud/index.php/s/qAa578RzBFf3znE#pdfviewer" 
                target="_blank" 
                @click="linkClicked('Directiva CITT', 'http://10.15.4.25/cloud/index.php/s/qAa578RzBFf3znE#pdfviewer')"
                class="submenu-link">
                Directiva CITT
                </a>

                <a href="http://10.15.4.25/cloud/index.php/s/PS5KEHqdnfHEy48#pdfviewer" 
                target="_blank"
                @click="linkClicked('Glosario Estadístico 2022', 'http://10.15.4.25/cloud/index.php/s/PS5KEHqdnfHEy48#pdfviewer')"
                class="submenu-link">
                Glosario Estadístico 2022
                </a>

                <a href="http://oasprod.essalud.gob.pe:7777/Referencia/servlet/Index" 
                target="_blank"
                @click="linkClicked('Referencias', 'http://oasprod.essalud.gob.pe:7777/Referencia/servlet/Index')"
                class="submenu-link">
                Referencias
                </a>

                <!--<a href="http://10.15.4.30:8080/seguimiento_anemia/#/users/login" target="_blank"
                @click="linkClicked('Seguimiento Anemia', 'http://10.15.4.30:8080/seguimiento_anemia/#/users/login')"
                class="submenu-link">Seguimiento Anemia</a>-->

                <a href="http://10.15.4.25/cloud/index.php/s/xS7A2L6MBTMoNxo#pdfviewer" 
                target="_blank"
                @click="linkClicked('Estándares CIE 10', 'http://10.15.4.25/cloud/index.php/s/xS7A2L6MBTMoNxo#pdfviewer')"
                class="submenu-link">
                Estándares CIE 10
                </a>

                <!--<a href="http://10.15.4.30:8080/gestantes/" target="_blank"
                @click="linkClicked('Sistema de Control de Gestantes', 'http://10.15.4.30:8080/gestantes/')"
                class="submenu-link">Sistema de Control de Gestantes</a>-->

            </div> <!--  FIN DE SUB MENU 'ASISTENCIALES'  -->

            <!--  TITULO DE 'ASISTENCIALES'  -->
            <div class="menu-header">
                <h2 class="subtitulo">
                    <img
                        src="../assets/icons/administrativos.png"
                        alt="Icono"
                        class="icon-before-subtitulo"
                    >
                    Administrativos
                </h2>
            </div> <!--  FIN DE TITULO DE 'ASISTENCIALES'  -->


        <div class="submenu">
        
        <!--<a href="#" target="_blank" class="submenu-link">Finanzas</a>-->

            <a href="https://sgd.essalud.gob.pe"
            target="_blank" 
            @click="linkClicked('SGD', 'https://sgd.essalud.gob.pe')"
            class="submenu-link">
            SGD
            </a>

            <a href="http://sgfa.essalud/" 
            target="_blank" 
            @click="linkClicked('SIAD', 'http://sgfa.essalud/')"
            class="submenu-link">
            SIAD
            </a>

            <!--<a href="#" target="_blank" @click="linkClicked('Oficina de Adquisiciones', '#')"
            class="submenu-link">Oficina de Adquisiciones</a>-->

            <a href="http://10.15.4.25/cloud/index.php/login" 
            target="_blank"
            @click="linkClicked('Nube/Cloud', 'http://10.15.4.25/cloud/index.php/login')"
            class="submenu-link">
            Nube/Cloud
            </a>

            <a href="http://intranet.essalud/portal/modules/news/" 
            target="_blank"
            @click="linkClicked('Intranet', 'http://intranet.essalud/portal/modules/news/')"
            class="submenu-link">
            Intranet
            </a>

            <!--<a href="http://172.27.58.100:8080/app.visitas/controlVisitas/index.php" target="_blank"
            @click="linkClicked('Registro Visitas', 'http://172.27.58.100:8080/app.visitas/controlVisitas/index.php')"
            class="submenu-link">Registro Visitas</a>-->

            <a href="https://mpv.essalud.gob.pe/Login/Index" 
            target="_blank"
            @click="linkClicked('Mesa de Partes Digital', 'https://mpv.essalud.gob.pe/Login/Index')"
            class="submenu-link">
            Mesa de Partes Digital
            </a>

            <a href="http://10.15.4.30:8080/scai/" 
            target="_blank"
            @click="linkClicked('SCAI', 'http://10.15.4.30:8080/scai/')" 
            class="submenu-link">
            SCAI
            </a>

            <a href="http://10.15.4.180:8080/privado/" 
            target="_blank"
            @click="linkClicked('Soporte Informático', 'http://10.15.4.180:8080/privado/')" 
            class="submenu-link">
            Soporte Informático
            </a>

        <!--<a href="#" @click="linkClicked('SCAI 2', '#')" class="submenu-link">SCAI 2</a>-->

        </div><!--  FIN DE SUB MENU 'ADMINISTRATIVOS'  -->

        <!--  INICIO DE SUB MENU 'RECURSOS HUMANOS'  -->
        <div class="menu-header">
            <h2 class="subtitulo">
            <img src="../assets/icons/recursos-humanos.png" 
                    alt="Icono" 
                    class="icon-before-subtitulo">
                    Recursos Humanos
            </h2>
        </div>

        <div class="submenu">

            <a href="http://10.15.4.30:8080/sgper2/" 
            target="_blank"
            @click="linkClicked('Boletas de Pago', 'http://10.15.4.30:8080/sgper2/')" 
            class="submenu-link">
            Boletas de Pago
            </a>

            <a href="http://10.56.1.114:7777/ficha/paginas/formularios_de_personal/ingreso.html" 
            target="_blank"
            @click="linkClicked('Ficha Única de Personal', 'http://10.56.1.114:7777/ficha/paginas/formularios_de_personal/ingreso.html')"
            class="submenu-link">
            Ficha Única de Personal
            </a>

            <a href="http://10.15.4.25/cloud/index.php/s/LydDXrLkqgoak99" 
            target="_blank"
            @click="linkClicked('Video Tutoriales', 'http://10.15.4.25/cloud/index.php/s/LydDXrLkqgoak99')"
            class="submenu-link">
            Video Tutoriales
            </a>

            <a href="http://10.15.4.25/cloud/index.php/s/py4BptdZ78iCNMX" 
            target="_blank"
            @click="linkClicked('Documentos de Gestión', 'http://10.15.4.25/cloud/index.php/s/py4BptdZ78iCNMX')"
            class="submenu-link">
            Documentos de Gestión
            </a>

        </div><!--  FIN DE SUB MENU 'RECURSOS HUMANOS'  -->

        <!--  INICIO DE SUB MENU 'CALIDAD'  -->
        <div class="menu-header">
            <h2 class="subtitulo">
            <img src="../assets/icons/calidad.png" 
                    alt="Icono" 
                    class="icon-before-subtitulo">
                    Calidad
            </h2>
        </div>

        <div class="submenu">

            <a href="http://supernova.essalud:8080/reginciad/index.html" 
            target="_blank"
            @click="linkClicked('Reginciad', 'http://supernova.essalud:8080/reginciad/index.html')"
            class="submenu-link">
            Reginciad
            </a>

            <a href="http://10.15.4.25/cloud/index.php/s/MTGK6dgHt5s7Hwj?path=%2FDIRECTIVAS" 
            target="_blank"
            @click="linkClicked('Directivas', 'http://10.15.4.25/cloud/index.php/s/MTGK6dgHt5s7Hwj?path=%2FDIRECTIVAS')"
            class="submenu-link">
            Directivas
            </a>

            <a href="http://10.15.4.25/cloud/index.php/s/MTGK6dgHt5s7Hwj?path=%2FGESTI%C3%93N%20POR%20PROCESOS"
            target="_blank"
            @click="linkClicked('Gestión por Procesos', 'http://10.15.4.25/cloud/index.php/s/MTGK6dgHt5s7Hwj?path=%2FGESTI%C3%93N%20POR%20PROCESOS')"
            class="submenu-link">
            Gestión por Procesos
            </a>

            <a href="http://10.15.4.25/cloud/index.php/s/MTGK6dgHt5s7Hwj?path=%2FINFORMES%20ANUALES" 
            target="_blank"
            @click="linkClicked('Informes Anuales', 'http://10.15.4.25/cloud/index.php/s/MTGK6dgHt5s7Hwj?path=%2FINFORMES%20ANUALES')"
            class="submenu-link">
            Informes Anuales
            </a>

            <a href="http://10.15.4.25/cloud/index.php/s/MTGK6dgHt5s7Hwj?path=%2FCONTROL%20INTERNO" 
            target="_blank"
            @click="linkClicked('Control Interno', 'http://10.15.4.25/cloud/index.php/s/MTGK6dgHt5s7Hwj?path=%2FCONTROL%20INTERNO')"
            class="submenu-link">
            Control Interno
            </a>

            <a href="http://10.15.4.25/cloud/index.php/s/MTGK6dgHt5s7Hwj?path=%2FGESTI%C3%93N%20DE%20LA%20CALIDAD"
            target="_blank"
            @click="linkClicked('Gestión de la Calidad', 'http://10.15.4.25/cloud/index.php/s/MTGK6dgHt5s7Hwj?path=%2FGESTI%C3%93N%20DE%20LA%20CALIDAD')"
            class="submenu-link">
            Gestión de la Calidad
            </a>

            <a href="http://10.15.4.25/cloud/index.php/s/MTGK6dgHt5s7Hwj?path=%2FIETSI" 
            target="_blank"
            @click="linkClicked('IETSI', 'http://10.15.4.25/cloud/index.php/s/MTGK6dgHt5s7Hwj?path=%2FIETSI')"
            class="submenu-link">
            IETSI
            </a>

            <a href="http://10.15.4.25/cloud/index.php/s/MTGK6dgHt5s7Hwj?path=%2FOBSERVACIONES%20SUSALUD" 
            target="_blank"
            @click="linkClicked('Observaciones Essalud', 'http://10.15.4.25/cloud/index.php/s/MTGK6dgHt5s7Hwj?path=%2FOBSERVACIONES%20SUSALUD')"
            class="submenu-link">
            Observaciones Essalud
            </a>

        </div><!--  FIN DE SUB MENU 'CALIDAD'  -->

        <!--  INICIO DE SUB MENU 'CONTINGENCIAS'  -->
        <div class="menu-header">
            <h2 class="subtitulo">
            <img src="../assets/icons/contingencia.png" 
                    alt="Icono" 
                    class="icon-before-subtitulo">
                    Contingencias
            </h2>
        </div>

        <div class="submenu">
            <a href="http://172.20.0.122:8080/cittcontrol/" 
            target="_blank"
            @click="linkClicked('SIGI', 'http://172.20.0.122:8080/cittcontrol/')" 
            class="submenu-link">
            SIGI
            </a>

            <a href="http://10.56.1.114:7777/Referencia/html/index.html" 
            target="_blank"
            @click="linkClicked('Referencias', 'http://10.56.1.114:7777/Referencia/html/index.html')"
            class="submenu-link">
            Referencias
            </a>

            <a href="http://10.56.1.158/sgss/servlet/hmain" 
            target="_blank"
            @click="linkClicked('SGSS', 'http://10.56.1.158/sgss/servlet/hmain')" 
            class="submenu-link">
            SGSS
            </a>

            <a href="http://10.0.0.217/" 
            target="_blank"
            @click="linkClicked('Sin Anemia - Contingencia', 'http://10.0.0.217/')" 
            class="submenu-link">
            Sin Anemia - Contingencia
            </a>

            <!--<a href="http://10.56.1.114:7777/cas/" target="_blank"
            @click="linkClicked('Disponibilidad Camas', 'http://10.56.1.114:7777/cas/')" class="submenu-link">Disponibilidad
            Camas</a>-->

            <a href="http://10.56.1.114:7777/perinat/" 
            target="_blank"
            @click="linkClicked('Vigilancia Perinatal - SVP', 'http://10.56.1.114:7777/perinat/')"
            class="submenu-link">
            Vigilancia Perinatal - SVP
            </a>

            <!--<a href="http://10.0.0.210/facturacion/login" target="_blank"
            @click="linkClicked('Facturación', 'http://10.0.0.210/facturacion/login')" class="submenu-link">Facturación</a>-->

            <a href="http://10.56.1.114:7777/acreditap/" 
            target="_blank"
            @click="linkClicked('Acreditación Intranet', 'http://10.56.1.114:7777/acreditap/')"
            class="submenu-link">
            Acreditación Intranet
            </a>

            <!--<a href="http://10.56.1.114:7777/acredita/" target="_blank"
            @click="linkClicked('Acreditación Internet', 'http://10.56.1.114:7777/acredita/')"
            class="submenu-link">Acreditación Internet</a>
            <a href="http://10.56.1.114:7777/transplante/" target="_blank"
            @click="linkClicked('Administración de Transplantes', 'http://10.56.1.114:7777/transplante/')"
            class="submenu-link">Administración de Transplantes</a>-->

            <a href="http://10.0.0.213/ANATPAT/" 
            target="_blank"
            @click="linkClicked('Anapat', 'http://10.0.0.213/ANATPAT/')"
            class="submenu-link">
            Anapat
            </a>

            <a href="http://172.20.0.120/POSTAS/" 
            target="_blank"
            @click="linkClicked('Inmunizaciones - SISCAP', 'http://172.20.0.120/POSTAS/')"
            class="submenu-link">
            Inmunizaciones - SISCAP
            </a>

            <a href="http://10.56.1.114:7779/acredita/" 
            target="_blank"
            @click="linkClicked('Acredita Internet Capcha', 'http://10.56.1.114:7779/acredita/')"
            class="submenu-link">
            Acredita Internet Capcha
            </a>

            <a href="http://10.56.1.114:7777/Sistema_Patrimonial_Contable/" 
            target="_blank"
            @click="linkClicked('Sistema Patrimonial Contable', 'http://10.56.1.114:7777/Sistema_Patrimonial_Contable/')"
            class="submenu-link">
            Sistema Patrimonial Contable
            </a>

            <a href="http://172.20.0.187/" 
            target="_blank" 
            @click="linkClicked('SIAD', 'http://172.20.0.187/')"
            class="submenu-link">
            SIAD
            </a>

            <a href="http://172.20.0.140/" 
            target="_blank" 
            @click="linkClicked('Infocal', 'http://172.20.0.140/')"
            class="submenu-link">
            Infocal
            </a>

            <!--<a href="http://10.56.1.114:7777/NewSicg/" target="_blank"
            @click="linkClicked('Inf. Centralizada - SICG', 'http://10.56.1.114:7777/NewSicg/')" class="submenu-link">Inf.
            Centralizada - SICG</a>-->

            <a href="http://10.56.1.127:9087/sgssqa/servlet/hmain" 
            target="_blank"
            @click="linkClicked('SGSS Pruebas', 'http://10.56.1.127:9087/sgssqa/servlet/hmain')"
            class="submenu-link">
            SGSS Pruebas
            </a>

        </div> <!--  FIN DE SUB MENU 'CONTINGENCIAS'  -->

    </div> <!--  FIN DE DROPDOWN DE OPCIONES DEL BOTON DE MENU  -->


    <!--  INICIO DE OPCIONES DE 'Formatos'  -->
    <div v-if="isFormatosOpen" class="menu-dropdown navbar-nav-scroll">

        <!--  INICIO DE SUB MENU 'Recursos Humanos'  -->
        <div class="menu-header">
            <h2 class="subtitulo">
            <img src="../assets/icons/recursos-humanos.png" 
            alt="Icono" 
            class="icon-before-subtitulo">
            Recursos Humanos
            </h2>
        </div>

        <div class="submenu">
            
            <a href="http://10.15.4.25/cloud/index.php/s/CtXDT8AAefKJkQd#pdfviewer" 
            target="_blank"
            @click="linkClicked('Formato Convenio y Vacaciones ', 'http://10.15.4.25/cloud/index.php/s/CtXDT8AAefKJkQd#pdfviewer')"
            class="submenu-link">
            Formato Convenio y Vacaciones 
            </a>

            <a href="http://10.15.4.180:8080/sgper/link/formatos/papeleta" 
            target="_blank"
            @click="linkClicked('Formato Papeletas de Permiso ', 'http://oasprod.essalud.gob.pe:7777/acreditap/')"
            class="submenu-link">
            Formato Papeletas de Permiso 
            </a>

        </div> <!--  FIN DE SUB MENU 'Recursos Humanos'  -->
        
        <div class="menu-header"> <!--  INICIO DE SUB MENU 'Soporte Informático - Formatos'  -->
            <h2 class="subtitulo">
            <img src="../assets/icons/laptop.png"
                    alt="Icono" 
                    class="icon-before-subtitulo">
                    Soporte Informático - Formatos
            </h2>
        </div>

        <div class="submenu">

            <a href="http://10.15.4.25/cloud/index.php/apps/onlyoffice/s/A6sqgQ3jeQc8mHq" 
            target="_blank"
            @click="linkClicked('Formato VPN  ', 'http://10.15.4.25/cloud/index.php/apps/onlyoffice/s/A6sqgQ3jeQc8mHq')"
            class="submenu-link">
            Formato VPN
            </a>

            <a href="http://10.15.4.25/cloud/index.php/s/C5k3CrbkKG7Gz2N" 
            target="_blank"
            @click="linkClicked('Formatos SIAD', 'http://10.15.4.25/cloud/index.php/s/C5k3CrbkKG7Gz2N')"
            class="submenu-link">
            Formatos SIAD
            </a>

            <a href="http://10.15.4.25/cloud/index.php/s/Anjj4HDY9Kn74k2" 
            target="_blank"
            @click="linkClicked('Formato para Acceso a Internet', 'http://10.15.4.25/cloud/index.php/s/Anjj4HDY9Kn74k2')"
            class="submenu-link">
            Formato para Acceso a Internet
            </a>

            <a href="http://10.15.4.25/cloud/index.php/s/3wkmyai4wmiA2xD" 
            target="_blank"
            @click="linkClicked('CAPACITACIÓN', 'http://10.15.4.25/cloud/index.php/s/3wkmyai4wmiA2xD')"
            class="submenu-link">
            CAPACITACIÓN
            </a>

            <a href="http://10.15.4.30:8080/sgper2/main-recibo-movilidad-caja-chica" 
            target="_blank"
            @click="linkClicked('FINANZAS - RECIBO DE CAJA CHICA', 'http://10.15.4.30:8080/sgper2/main-recibo-movilidad-caja-chica')"
            class="submenu-link">
            FINANZAS - RECIBO DE CAJA CHICA 
            </a>

        </div> <!--  FIN DE SUB MENU 'Soporte Informático - Formatos'  -->

    </div>

    <!--  INICIO DE OPCIONES DE 'Transparencia'  -->
    <div v-if="isTransparenciaOpen" class="menu-dropdown navbar-nav-scroll">

        <div class="submenu">

            <a href="http://10.15.4.30:8080/scai/externos/Main/ConsultaCI" 
            target="_blank"
            @click="linkClicked('Consulta de Correos Essalud', 'http://10.15.4.30:8080/scai/externos/Main/ConsultaCI')"
            class="submenu-link">
            Consulta de Correos Essalud
            </a>

            <a href="http://10.15.4.30:8080/scai/externos/Main/ConsultaRPM" 
            target="_blank"
            @click="linkClicked('Directorio RPM', 'http://10.15.4.30:8080/scai/externos/Main/ConsultaRPM')"
            class="submenu-link">
            Directorio RPM
            </a>

            <a href="http://intranet.essalud/portal/directorio/DIRECTORIO_sede_central.pdf" 
            target="_blank"
            @click="linkClicked('Directorio - Sede Central', 'http://intranet.essalud/portal/directorio/DIRECTORIO_sede_central.pdf')"
            class="submenu-link">
            Directorio - Sede Central
            </a>

            <a href="http://www.essalud.gob.pe/downloads/compendio-essalud/normas_internas/DIRECTIVAS.pdf" 
            target="_blank"
            @click="linkClicked('Compendio de Directivas', 'http://www.essalud.gob.pe/downloads/compendio-essalud/normas_internas/DIRECTIVAS.pdf')"
            class="submenu-link">
            Compendio de Directivas
            </a>

            <a href="http://10.15.4.30:8080/scai/externos/XbuscarActivosAsignados/principal/" 
            target="_blank"
            @click="linkClicked('Busque sus equipos asignados', 'http://10.15.4.30:8080/scai/externos/XbuscarActivosAsignados/principal/')"
            class="submenu-link">
            Busque sus equipos asignados
            </a>

            <a href="http://10.15.4.30:8080/scai/externos/Main/ConsultaAnexosIP" 
            target="_blank"
            @click="linkClicked('Directorio de anexos institucionales', 'http://10.15.4.30:8080/scai/externos/Main/ConsultaAnexosIP')"
            class="submenu-link">
            Directorio de anexos institucionales
            </a>

            <a href="http://intranet.essalud/portal/directorio/DIRECTORIO_Redes_Provincias.pdf" 
            target="_blank"
            @click="linkClicked('Directorio - Redes Asistenciales', 'http://intranet.essalud/portal/directorio/DIRECTORIO_Redes_Provincias.pdf')"
            class="submenu-link">
            Directorio - Redes Asistenciales
            </a>

            <a href="http://10.15.4.25/cloud/index.php/s/eYBTyDk456rdRn4" 
            target="_blank"
            @click="linkClicked('Resoluciones', 'http://10.15.4.25/cloud/index.php/s/eYBTyDk456rdRn4')"
            class="submenu-link">
            Resoluciones
            </a>

            <!--<a href="http://10.15.4.25/cloud/index.php/s/KrybTYSyfHJobpS" target="_blank"
            @click="linkClicked('Seguridad y salud en el trabajo', 'http://10.15.4.25/cloud/index.php/s/KrybTYSyfHJobpS')"
            class="submenu-link">Seguridad y salud en el trabajo</a>
            <a href="http://172.27.58.100:8080/app.visitas/reportes/vistas/consultavisita.php" target="_blank"
            @click="linkClicked('Reporte de Visitas ', 'http://172.27.58.100:8080/app.visitas/reportes/vistas/consultavisita.php')"
            class="submenu-link">Reporte de Visitas </a>-->
        </div>

    </div><!--  FIN DE OPCIONES DE 'Transparencia'  -->
    
</div>

</header>

</template>

<style scoped>

/* Contenedor del logo del menu principal*/
.logo-container{
    display: flex;
    align-items: center;
    margin-left: 2vw;
    max-width: 100%;
    text-align: center;
}
/* Clase de la imagen de ESSALUD */
.logo-img {
    margin-right: 10px; /* Espacio entre la imagen del logo y el texto */
}

/* Propiedades para el espaciado de las opciones del menu */
.navbar-nav .nav-item {
    margin-right: 15px; /* Espaciado entre las opciones del menú */
    }
.icon-spacing {
    margin-right: 15px; /* Espaciado uniforme para los íconos */
    }
.nav-item.d-flex {
    display: flex;
    align-items: center; /* Alinea verticalmente los íconos con el texto */
}

/* Propiedades para el dropdown de opciones del menu */
.menu-dropdown {
    position: absolute;
    background-color: #ffffff;
    box-shadow: 0px 2px 4px rgba(0, 0, 0, 0.1);
    padding: 1rem;
    width: 80%;
    display: flex;
    flex-direction: column;
    top: 61px;
    left: 50%;
    transform: translateX(-50%);
    overflow-y: auto;
}
.menu-dropdown a {
    color: #333;
    text-decoration: none;
    font-size: 1rem;
}

/* Propiedades para el titulo de menu*/
.menu-header {
    margin-bottom: 10px;
    display: flex;
    align-items: center;
    justify-content: space-between; /* Alinea el título y el botón de cerrar */
}

/* Propiedades para la clase de subtitulo */
.subtitulo {
    font-size: 1.2rem;
    margin: 0;
    color: #0055cc;
    display: flex;
    align-items: center;
    font-family: Arial;
    font-size: 1.3rem;
    font-weight: 300;
}

/* Opciones de la calse submenu */
.submenu {
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    gap: 0.7rem;
    padding: 0.3rem;
}
.submenu a {
    color: #4d4b4b;
    text-decoration: none;
    font-family: Arial;
    text-align: left;
    }
.submenu-link {
    position: relative;
    text-decoration: none;
    color: #333;
}
.submenu-link::before {
    content: "";
    position: absolute;
    left: 0;
    bottom: -2px;
    width: 0;
    height: 3px;
    background-color: #0055cc;
    transform-origin: bottom left;
    transition: width 0.3s ease-in-out;
}
.submenu-link:hover::before {
    content: attr(data-content);
    width: 50%;
}

/* Propiedades para la clase icon-before-subtitulo */
.icon-before-subtitulo {
    width: 30px;
    height: 30px;
    margin-right: 0.5rem;
    cursor: pointer; /* Cambia el cursor para indicar que es interactivo */
}

/* Propiedades el boton de expansion */
.btn-expand-compress {
    background-color: #007bff;
    color: white;
    border: none;
    padding: 8px 12px;
    margin: 10px 0;
    cursor: pointer;
    border-radius: 4px;
}
.btn-expand-compress:hover {
    background-color: #0056b3;
}
    
/* Propiedades la clase del boton cerrar */
.icon-close {
    width: 25px; /* Tamaño del ícono de cerrar */
    height: 25px;
}

/* Propiedad de la clase btn-close-menu */
.btn-close-menu {
    position: absolute; /* Posiciona el botón relativo al contenedor */
    top: 10px; /* Separación desde la parte superior */
    right: 10px; /* Separación desde la parte derecha */
    background: none;
    border: none;
    cursor: pointer;
}

/* Propiedades para la clase del menu */
.menu-header {
    display: flex;
    justify-content: space-between;
    align-items: flex-end;
    background-color: #eaf1fa;
    text-align: right;
}

/* Propiedades para el fondo */
.overlay {
    position: fixed;
    top: 0px;
    left: 0px;
    width: 100%;
    height: flex;
    background-color: #ffffff;
    z-index: 2;
}

/* Propiedades para el titulo*/
.title {
    color: #0076c7;              /* Color azul del texto */
    font-family: Arial, sans-serif; /* Fuente Arial con respaldo */
    font-weight: 400;            /* Peso normal para el texto */
    font-size: 23px;             /* Tamaño de fuente */
    margin: 0;                   /* Elimina márgenes innecesarios */
    white-space: nowrap;         /* Evita que el texto se divida en líneas */
    display: flex;               /* Usar flexbox para control adicional */
    align-items: center;         /* Alineación vertical centrada */
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
