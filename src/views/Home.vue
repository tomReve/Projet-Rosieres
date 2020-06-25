<template>
  <div class="home">
    <input-xls @change="dataChange" />
    <section id="pagination">
      <p>Afficher</p>
      <v-select :options="elementsPerPageOptions" :value="elementsPerPage" :searchable="false" @input="elementsPerPageChange"/>
      <p>personnes par page</p>
    </section>
    <pagination :page="page" :pages="numberOfPages" @change="paginationChange"/>
    <table>
      <thead v-for="data in rawData" :key="data.__EMPTY">
          <tr v-if="data.__EMPTY == 1">
            <th>Prénom</th>
            <th>Nom</th>
            <th>Genre</th>
            <th>Pays</th>
            <th>Âge</th>
            <th>Date</th>
            <th>Id</th>
            <!-- <th v-for="(element, index) in data" :key="element.key">{{index}}</th> -->
          </tr>
          <tr :key="data.__EMPTY" v-if="data.__EMPTY == 1">
            <th>
              <input type="text" v-model="filterFirstName">
            </th>
            <th>
              <input type="text" v-model="filterLastName">
            </th>
            <th>
              <v-select/>
            </th>
            <th>

            </th>
          </tr>
      </thead>
      <tbody>
        <tr v-for="data in pageRawData" :key="data.__EMPTY">
          <td>{{data['First Name']}}</td>
          <td>{{data['Last Name']}}</td>
          <td>{{data['Gender']}}</td>
          <td>{{data['Country']}}</td>
          <td>{{data['Age']}}</td>
          <td>{{data['Date']}}</td>
          <td>{{data['Id']}}</td>
        </tr>
      </tbody>
    </table>
    <pagination :page="page" :pages="numberOfPages" @change="paginationChange"/>
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
        filterFirstName:null,
        filterLastName:null
      }
    },
    computed:{
      pageRawData(){
        const firstElement = this.page*this.elementsPerPage - this.elementsPerPage;
        const lastElement = this.page*this.elementsPerPage;
        let data = this.rawData.slice(firstElement,lastElement);
        if(this.filterFirstName) {
          data = data.filter(element => (element['First Name'].toLowerCase().includes(this.filterFirstName.toLowerCase())));
        }
        if(this.filterLastName) {
          data = data.filter(element => (element['Last Name'].toLowerCase().includes(this.filterLastName.toLowerCase())));
        }
        return data;
      },
      numberOfPages(){
        return this.rawData.length/this.elementsPerPage;
      }
    },
    mounted(){
      this.elementsPerPage = this.elementsPerPageOptions[0];
      if(localStorage.rawData) {
        this.rawData = JSON.parse(localStorage.rawData);
      }
    },
    methods: {
      dataChange(e) {
        this.rawData = e;
        localStorage.rawData = JSON.stringify(this.rawData);
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

<style lang="scss">
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