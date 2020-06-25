<template>
  <div class="home">
    <img class="header" alt="Vue logo" src="./../assets/header-bannier.jpg">
    <input-xls @change="dataChange" />
    <table>
      <thead>
        <template v-for="data in rawData">
          <tr :key="data.__EMPTY" v-if="data.__EMPTY == 1">
            <th v-for="(element, index) in data" :key="element.key">{{index}}</th>
          </tr>
        </template>
      </thead>
      <tbody>
        <template v-for="data in rawData">
          <tr :key="data.__EMPTY">
            <td v-for="(element, index) in data" :key="element.key">{{data[index]}}</td>
          </tr>
        </template>
      </tbody>
    </table>
  </div>
</template>

<script>
  import InputXLS from '@/components/InputXLS'

  export default {
    name: 'Home',
    components: {
      'input-xls': InputXLS
    },
    data() {
      return {
        rawData: [],
      }
    },
    methods: {
      dataChange(e) {
        this.rawData = e;
      }
    }
  }
</script>

<style lang="css" scoped>
@font-face {
	font-family: "Lucida Grande Regular";
	src: url('./../assets/Lucida_Grande_Regular.ttf');
}

.home {
  display: flex;
  flex-direction: column;
  width: 100%;
  height: 100%;
  font-family: "Lucida Grande Regular";
}

.home .header {
  display: flex;
  width: 100%;
  height: auto;
}

.home table {
  border-collapse: collapse;
  width: 90%;
  margin: auto;
  color: #017EFD;
}

.home thead th {
  display: flex;
  justify-content: center;
  align-items: center;
}

.home thead tr {
  background-color: #017EFD14;
}

.home tr {
  display: flex;
  height: 7vh;
  text-align: center;
  justify-content: center;
  align-items: center;
}

.home tbody tr:first-child {
  border-top: 1px solid #017EFD73;
}

.home tbody tr {
  border: 1px solid #017EFD73;
  border-top: none;
}

.home tbody td {
  display: flex;
  justify-content: center;
  align-items: center;
  border: 1px solid #017EFD73;
  border-left: none;
  border-top: none;
  border-bottom: none;
}

.home tbody tr td:last-child {
  border-right: none;
}

.home thead th, .home tbody td {
  width: 100%;
  height: 50%;
}
</style>