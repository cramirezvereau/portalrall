<script>
import { ref } from "vue";
import { PDFDocument, rgb } from "pdf-lib";
import fontkit from "@pdf-lib/fontkit";

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
      salida: "",
      regreso: "",
      observaciones: "",
      sinGoce: false,
      vacaciones: false,
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

    const loadAndFillPDF = async () => {
      const existingPdfBytes = await fetch("/portalweb/pdfs/papeletasEditableOK.pdf").then(res => res.arrayBuffer());
    const pdfDoc = await PDFDocument.load(existingPdfBytes);
    pdfDoc.registerFontkit(fontkit);
    
    const pages = pdfDoc.getPages();
    const firstPage = pages[0];

    const fontSize = 12;
    const textColor = rgb(0, 0, 0);

    // Agregar texto en posiciones específicas
    firstPage.drawText(formData.value.jefePersonal, { x: 175, y: 695, size: fontSize, color: textColor });
    firstPage.drawText(formData.value.nombre, { x: 127, y: 659, size: fontSize, color: textColor });
    firstPage.drawText(formData.value.servicio, { x: 103, y: 632, size: fontSize, color: textColor });
    firstPage.drawText(formData.value.permisoPor, { x: 145, y: 605, size: fontSize, color: textColor });

    firstPage.drawText(formData.value.fechaInicio, { x: 160, y: 570, size: fontSize, color: textColor });
    firstPage.drawText(formData.value.fechaFin, { x: 300, y: 570, size: fontSize, color: textColor });

    //firstPage.drawText(formData.value.motivo, { x: 85, y: 543, size: fontSize, color: textColor }); //listo hasta aqui

    //firstPage.drawText(formData.value.fecha, { x: 145, y: 460, size: fontSize, color: textColor });

    firstPage.drawText(formData.value.salida, { x: 150, y: 182, size: fontSize, color: textColor });//listo
    firstPage.drawText(formData.value.regreso, { x: 150, y: 160, size: fontSize, color: textColor });//listo

    firstPage.drawText(formData.value.observaciones, { x: 168, y: 137, size: fontSize, color: textColor });//listo

    // Checkbox de permisos generales
    if (formData.value.sinGoce) firstPage.drawText("X", { x: 180, y: 378, size: fontSize, color: textColor });
    if (formData.value.vacaciones) firstPage.drawText("X", { x: 182, y: 355, size: fontSize, color: textColor });

    // Coordenadas específicas para cada checkbox
    const checkboxPositions = {
        "Por enfermedad": { x: 237, y: 310 },
        "Por capacitación": { x: 235, y: 294 },
        "Por fallecimiento de familiares": { x: 300, y: 279 },
        "Permiso particular": { x: 246, y: 264 },
        "Permiso personal": { x: 242, y: 249 },
        "Por onomástico": { x: 234, y: 234 },
        "Por compensación": { x: 246, y: 219 },
        "Por comisión de servicios": { x: 277, y: 204 }
    };

    // Marcar checkboxes en el PDF
    formData.value.sinDescuentos.forEach((item) => {
        if (item.checked && checkboxPositions[item.label]) {
            firstPage.drawText("X", { 
                x: checkboxPositions[item.label].x, 
                y: checkboxPositions[item.label].y, 
                size: fontSize, 
                color: textColor 
            });
        }
    });
    
    // Para la fecha en formato "Chao, 01 de Enero del 2022"
    const formatDate = (dateString) => {
      if (!dateString) return "";

      const months = [
        "Enero", "Febrero", "Marzo", "Abril", "Mayo", "Junio",
        "Julio", "Agosto", "Septiembre", "Octubre", "Noviembre", "Diciembre"
      ];

      // Extraer año, mes y día manualmente
      const [year, month, day] = dateString.split("-");

      return `Chao, ${parseInt(day)} de ${months[parseInt(month) - 1]} del ${year}`;
    };

    // Agregar la fecha en formato "Chao, 01 de Enero del 2022"
    const formattedDate = formatDate(formData.value.fecha);
    firstPage.drawText(formattedDate, { x: 85, y: 480, size: fontSize, color: textColor });

    // Función para dibujar texto envuelto en varias líneas
    const drawWrappedText = (page, text, x, y, maxCharsPerLine, lineHeight) => {
      const words = text.split(" ");
      let lines = [""];
      let lineIndex = 0;

      words.forEach(word => {
        if ((lines[lineIndex] + " " + word).length > maxCharsPerLine) {
          lineIndex++;
          lines[lineIndex] = word;
        } else {
          lines[lineIndex] += " " + word;
        }
      });

      lines.forEach((line, i) => {
        page.drawText(line.trim(), { x, y: y - i * lineHeight, size: 12, color: rgb(0, 0, 0) });
      });
    };

    // Llamar la función asegurando que el PDF se genera correctamente
    drawWrappedText(firstPage, formData.value.motivo, 85, 543, 80, 14);
    /*
      firstPage → Página del PDF donde se escribirá el texto.
      formData.value.motivo → Texto que ingresó el usuario en el formulario.
      85, 543 → Coordenadas donde comenzará el texto.
      50 → Máximo de caracteres por línea antes de hacer un salto.
      14 → Espaciado entre líneas.
    */ 

    // Guardar y descargar el PDF
    const pdfBytes = await pdfDoc.save();
    const blob = new Blob([pdfBytes], { type: "application/pdf" });
    const link = document.createElement("a");
    link.href = URL.createObjectURL(blob);
    link.download = "Formato_Papeleta.pdf";
    link.click(); 
    };

    return {
      formData,
      loadAndFillPDF
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
          <label class="form-label">Fecha:</label>
          <input v-model="formData.fecha" type="date" class="form-control" />
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

    <button class="btn btn-primary mt-3" @click="loadAndFillPDF">Generar PDF</button>
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
</style>
