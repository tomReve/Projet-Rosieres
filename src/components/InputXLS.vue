<template>
  <div class="btn-file">
    <label>
      <img src="./../assets/arrow-circle-down-solid.svg">
      Importer données
      <input type="file" @change="uploadFile"/>
    </label>
  </div>

</template>

<script>
  import XLSX from 'xlsx';
  export default {
    name: 'input-xls',
    methods: {
      uploadFile(e) {
        var files = e.target.files,
          f = files[0];
        var reader = new FileReader();
        const these = this;
        reader.onload = function (e) {
          var data = new Uint8Array(e.target.result);
          var workbook = XLSX.read(data, {
            type: 'array'
          });
          let sheetName = workbook.SheetNames[0]
          let worksheet = workbook.Sheets[sheetName];
          these.$emit('change', XLSX.utils.sheet_to_json(worksheet));
        };
        reader.readAsArrayBuffer(f);
      }
    }
  }
</script>

<style lang="css">
 .btn-file {
    display: flex;
    min-width: 15vw;
    width: min-content;
    height: 5vh;
    background-color: #01FD771F;
    border: solid 2px #00BF5A;
    border-radius: 2vh;
    cursor: pointer;
  }

  .btn-file label {
    display: flex;
    width: 100%;
    overflow: hidden;
    position: relative;
    text-align: center;
    cursor: pointer;
    align-items: center;
    justify-content: center;
    color: #00BF5A;
    cursor: pointer;
  }

  .btn-file label img {
    display: flex;
    height: 3vh;
    width: auto;
    margin-right: 10%;
    cursor: pointer;
  }

  .btn-file label input[type="file"] {
    cursor: pointer;
    display: block;
    font-size: 999px;
    filter: alpha(opacity=0);
    height: 100%;
    width: 100%;
    opacity: 0;
    position: absolute;
    right: 0;
    text-align: right;
    top: 0;
  }

</style>
