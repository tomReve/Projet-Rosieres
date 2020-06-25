<template>
  <div class="home">
    <img class="header" alt="Vue logo" src="./../assets/header-bannier.jpg">
    <input-xls @change="dataChange" />
    <section id="pagination">
      <p>Afficher</p>
      <v-select :options="elementsPerPageOptions" :value="elementsPerPage" :searchable="false" @input="elementsPerPageChange"/>
      <p>personnes par page</p>
      <pagination :page="page" :pages="numberOfPages" @change="paginationChange"/>
    </section>
    <table>
      <thead>
        <template v-for="data in rawData">
          <tr :key="data.__EMPTY" v-if="data.__EMPTY == 1">
            <th v-for="(element, index) in data" :key="element.key">{{index}}</th>
          </tr>
        </template>
      </thead>
      <tbody>
        <tr v-for="data in pageRawData" :key="data.__EMPTY">
          <td>{{data['Id']}}</td>
          <td>{{data['First Name']}}</td>
          <td>{{data['Last Name']}}</td>
          <td>{{data['Gender']}}</td>
          <td>{{data['Country']}}</td>
          <td>{{data['Age']}}</td>
          <td>{{data['Date']}}</td>
        </tr>
      </tbody>
    </table>
    <pagination :page="page" :pages="numberOfPages" @change="paginationChange"/>
    <p>{{ pageRawData }}</p>
  </div>
</template>

<script>
  import InputXLS from '@/components/InputXLS';
  import Pagination from '@/components/Pagination';
  import vSelect from 'vue-select'

  export default {
    name: 'Home',
    components: {
      'input-xls': InputXLS,
      'pagination': Pagination,
      'v-select':vSelect
    },
    data() {
      return {
        rawData: [],
        elementsPerPageOptions:[10,25,50],
        elementsPerPage:null,
        page:1,

      }
    },
    computed:{
      pageRawData(){
        const firstElement = this.page*this.elementsPerPage - this.elementsPerPage;
        const lastElement = this.page*this.elementsPerPage;
        return this.rawData.slice(firstElement,lastElement);
      },
      numberOfPages(){
        return this.rawData.length/this.elementsPerPage;
      }
    },
    mounted(){
      this.elementsPerPage = this.elementsPerPageOptions[0];
    },
    methods: {
      dataChange(e) {
        this.rawData = e;
      },
      elementsPerPageChange(e){
        this.elementsPerPage = e;
        this.page = 1;
      },
      paginationChange(e){
        this.page = e;
      }
    }
  }
</script>

<style lang="scss" scoped>
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

@import "vue-select/src/scss/vue-select.scss";
.home {
  width: 100%;

  #pagination {
    display: flex;
    align-items: center;

    .v-select {
      width: 10%;
      margin: 0 1%;
    }
  }
}
</style>