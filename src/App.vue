<template>
  <v-app>
    <v-app-bar
      app
      color="primary"
      dark
    >
      <div class="d-flex align-center">
        <v-img
          alt="Vuetify Logo"
          class="shrink mr-2"
          contain
          src="https://cdn.vuetifyjs.com/images/logos/vuetify-logo-dark.png"
          transition="scale-transition"
          width="40"
        />

        <v-img
          alt="Vuetify Name"
          class="shrink mt-1 hidden-sm-and-down"
          contain
          min-width="100"
          src="https://cdn.vuetifyjs.com/images/logos/vuetify-name-dark.png"
          width="100"
        />
      </div>

      <v-spacer></v-spacer>

      <v-btn
        href="https://github.com/vuetifyjs/vuetify/releases/latest"
        target="_blank"
        text
      >
        <span class="mr-2">Latest Release</span>
        <v-icon>mdi-open-in-new</v-icon>
      </v-btn>
    </v-app-bar>

    <v-main class="pa-6 mt-15">

      <h1 class="text-center">CARGA DE DATOS MASIVOS</h1>

      <v-row class="mt-5">

        <v-col cols="4" class="ma-auto">
          <v-file-input
            label="Ingrese el Archivo"
            outlined
            dense
            show-size
            small-chips
            cursor:pointer
            color="deep-purple accent-4"
            @change="handleFileChange"
          ></v-file-input>
        </v-col>

        <v-col cols="12">

          <div v-if="jsonData.length > 0">


            <v-data-table
              :headers="headers"
              :items="transformData(filteredData)"
              :items-per-page="10"
              class="elevation-1"
            >
            </v-data-table>


            <h2>Datos del Archivo Excel:</h2>

            <table>
            <thead><tr><th>ID Pregunta</th><th>ID Categoria</th></tr></thead>
            <!-- The `tbody` section includes the data rows -->
            <tbody>
              <!-- generate row (TR) for each president -->
              <tr v-for="(row, index) in filteredData" :key="index">
                <td>{{ row[0] }}</td>
                <td>{{ row[1] }}</td>
              </tr>
            </tbody>
          </table>



            <ul>
              <li v-for="(row, index) in filteredData" :key="index">
                {{ row[0] }}
              </li>
            </ul>
          </div>
        </v-col>

      </v-row>
    </v-main>
  </v-app>
</template>

<script>
//FFOutputimport { read, utils, writeFile } from 'xlsx';
import { read, utils } from 'xlsx';

export default {
  name: 'App',
  data: () => ({
      jsonData: [],
      headers: [],
  }),
  mounted() {
    // Ejemplo de cómo usar SheetJS para leer un archivo Excel
    /*const workbook = XLSX.readFile('preguntas.xls');
    const sheetName = workbook.SheetNames[0];
    const sheet = workbook.Sheets[sheetName];
    const data = XLSX.utils.sheet_to_json(sheet, { header: 1 });

    console.log(data); */
  },
  computed: {
   /* headers() {
      // Crea encabezados basados en la primera fila del archivo
      return this.jsonData[0];
    },*/
    filteredData() {
      // Filtra la primera fila y las filas que no tienen un número en la primera columna
      return this.jsonData.slice(1);
    }
  },
  methods: {
    handleFileChange(event) {
      let file = event;
      if (file) {
        this.readFile(file);
      }
    },
    transformData(data) {
        // Transforma cada arreglo en un objeto con propiedades numéricas
        return data.map(row => {
            const obj = {};
            this.headers.forEach((header, index) => {
            obj[header] = row[index];
            });
            return obj;
        });
    },
    readFile(file) {
      let reader = new FileReader();

      reader.onload = (e) => {
        let data = new Uint8Array(e.target.result);
        let workbook = read(data,{ type: 'array' });
        let sheetName = workbook.SheetNames[0];
        let sheet = workbook.Sheets[sheetName];
        let jsonData = utils.sheet_to_json(sheet, { header: 1 });

        

        this.jsonData = jsonData.filter(row => row.length > 0);

        console.log("DATA: ",this.jsonData);
        console.log("HEADERS: ",this.jsonData[0]);
      };

      reader.readAsArrayBuffer(file);
    }
  }
};
</script>
