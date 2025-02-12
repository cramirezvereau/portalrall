
<script>
import { ref } from "vue";
import html2canvas from "html2canvas";
import jsPDF from "jspdf";

export default {
  setup() {
    const formData = ref({
      jefePersonal: "",
      nombre: "",
      servicio: "",
      permisoPor: "",
      fechaInicio: "",
      fechaFin: "",
      motivo: "",
      fecha: "",
      firmaJefe: "",
      firmaSolicitante: "",
      salida: "",
      regreso: "",
      observaciones: "",
      sinDescuentos: [
        { label: "Por enfermedad", checked: false },
        { label: "Por capacitación", checked: false },
        { label: "Por fallecimiento de familiares", checked: false },
        { label: "Permiso particular", checked: false },
        { label: "Permiso personal", checked: false },
        { label: "Por onomástico", checked: false },
        { label: "Por compensación", checked: false },
        { label: "Por comisión de servicios", checked: false }
      ]
    });

    const generatePDF = () => {
      const formElement = document.getElementById("formularioSalida");
      html2canvas(formElement, { scale: 3, ignoreElements: (element) => element.tagName === 'BUTTON' }).then((canvas) => {
        const imgData = canvas.toDataURL("image/png");
        const pdf = new jsPDF("p", "mm", "a4");
        pdf.addImage(imgData, "PNG", 10, 10, 190, 280);
        pdf.save("Formato_Papeleta.pdf");
      });
    };

    return {
      formData,
      generatePDF
    };
  }
};
</script>

<template>
    <div class="form-container">
      <div id="formularioSalida" class="p-4 border rounded">
        <div class="text-center">
          <img src="../assets/icons/logoform.png" alt="Logo EsSalud" class="logo" />
          <h3>RED ASISTENCIAL LA LIBERTAD</h3>
          <h4>HOSPITAL BICENTENARIO CHAO</h4>
          <h5>PAPELETA DE AUTORIZACIÓN DE SALIDA</h5>
        </div>
        
        <div class="mb-3">
          <label class="form-label">Sr Jefe de Personal:</label>
          <input v-model="formData.jefePersonal" type="text" class="form-control" />
        </div>
        <div class="mb-3">
          <label class="form-label">Don(ña):</label>
          <input v-model="formData.nombre" type="text" class="form-control" />
        </div>
        <div class="mb-3">
          <label class="form-label">Quien presta servicios en:</label>
          <input v-model="formData.servicio" type="text" class="form-control" />
        </div>
        <div class="mb-3">
          <label class="form-label">A órdenes del suscrito, solicita permiso por:</label>
          <input v-model="formData.permisoPor" type="text" class="form-control" />
        </div>
        <div class="mb-3 d-flex">
          <div class="me-2">
            <label class="form-label">A partir del:</label>
            <input v-model="formData.fechaInicio" type="date" class="form-control" />
          </div>
          <div>
            <label class="form-label">Hasta:</label>
            <input v-model="formData.fechaFin" type="date" class="form-control" />
          </div>
        </div>
        <div class="mb-3">
          <label class="form-label">Por asuntos motivados:</label>
          <textarea v-model="formData.motivo" class="form-control"></textarea>
        </div>
        <div class="mb-3">
          <label class="form-label">Chao, Fecha:</label>
          <input v-model="formData.fecha" type="date" class="form-control" />
        </div>
        <div class="mb-3">
          <label class="form-label">Firma del Jefe inmediato:</label>
          <input type="file" @change="uploadSignature($event, 'firmaJefe')" class="form-control" />
          <img v-if="formData.firmaJefe" :src="formData.firmaJefe" class="firma-preview" />
        </div>
        <div class="mb-3">
          <label class="form-label">Firma del solicitante:</label>
          <input type="file" @change="uploadSignature($event, 'firmaSolicitante')" class="form-control" />
          <img v-if="formData.firmaSolicitante" :src="formData.firmaSolicitante" class="firma-preview" />
        </div>
        <h5>CONTROL DE CALIFICACIÓN:</h5>
        <div class="mb-3">
          <label class="form-label">Sin goce de haber</label>
          <input v-model="formData.sinGoce" type="checkbox" class="form-check-input" />
        </div>
        <div class="mb-3">
          <label class="form-label">Acta de vacaciones</label>
          <input v-model="formData.vacaciones" type="checkbox" class="form-check-input" />
        </div>
        <h6>Sin descuentos:</h6>
        <div class="mb-3" v-for="(item, key) in formData.sinDescuentos" :key="key">
          <label class="form-label">{{ item.label }}</label>
          <input v-model="item.checked" type="checkbox" class="form-check-input" />
        </div>
        <div class="mb-3">
          <label class="form-label">Salida:</label>
          <input v-model="formData.salida" type="date" class="form-control" />
        </div>
        <div class="mb-3">
          <label class="form-label">Regreso:</label>
          <input v-model="formData.regreso" type="date" class="form-control" />
        </div>
        <div class="mb-3">
          <label class="form-label">Observaciones:</label>
          <textarea v-model="formData.observaciones" class="form-control"></textarea>
        </div>
      </div>

      <button class="btn btn-primary mt-3" @click="generatePDF">Generar PDF</button>
    
    </div>
  </template>
  
  <style>
  .form-container {
    max-width: 700px;
    margin: auto;
  }
  .logo {
    width: 150px;
    display: block;
    margin: auto;
  }
  .firma-preview {
    width: 150px;
    height: auto;
    margin-top: 10px;
  }
  </style>
  