<template>
    <v-data-table
      :headers="headers"
      :items="filteredData"
      :items-per-page="5"
      class="elevation-1"
    ></v-data-table>
  </template>
  
  <script>
  import XLSX from 'xlsx';
  
  export default {
    data() {
      return {
        headers: [],
        jsonData: [],
      };
    },
    computed: {
      filteredData() {
        return this.jsonData.slice(1).map(row => {
          const obj = {};
          this.headers.forEach((header, index) => {
            obj[header.value] = row[index];
          });
          return obj;
        });
      },
    },
    methods: {
      handleFileChange(event) {
        const file = event.target.files[0];
        if (file) {
          this.readFile(file);
        }
      },
      readFile(file) {
        const reader = new FileReader();
  
        reader.onload = (e) => {
          const data = new Uint8Array(e.target.result);
          const workbook = XLSX.read(data, { type: 'array' });
  
          const sheetName = workbook.SheetNames[0];
          const sheet = workbook.Sheets[sheetName];
          this.jsonData = XLSX.utils.sheet_to_json(sheet, { header: 1 });
  
          if (this.jsonData.length > 0) {
            // Construir las columnas basadas en la primera fila de datos
            this.headers = Object.keys(this.jsonData[0]).map(key => ({
              text: key,
              align: 'start',
              sortable: true,
              value: key,
            }));
          }
        };
  
        reader.readAsArrayBuffer(file);
      },
    },
  };
  </script>
  