<template>
    <input class="input-xls" type="file" @change="uploadFile">
</template>

<script>
import XLSX from 'xlsx';
export default {
  name: 'input-xls',
  methods: {
    uploadFile(e) {
          var files = e.target.files, f = files[0];
          var reader = new FileReader();
          const these = this;
          reader.onload = function(e) {
            var data = new Uint8Array(e.target.result);
            var workbook = XLSX.read(data, {type: 'array'});
            let sheetName = workbook.SheetNames[0]
            let worksheet = workbook.Sheets[sheetName];
            these.$emit('change',XLSX.utils.sheet_to_json(worksheet));
          };
          reader.readAsArrayBuffer(f);
    }
  }
}
</script>