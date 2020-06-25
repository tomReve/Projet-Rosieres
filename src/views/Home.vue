<template>
  <div class="home">
    <input-xls @change="dataChange" />
    <section id="pagination">
      <p>Afficher</p>
      <v-select :options="elementsPerPageOptions" :value="elementsPerPage" :searchable="false" @input="elementsPerPageChange"/>
      <p>personnes par page</p>
      <pagination :page="page" :pages="numberOfPages" @change="paginationChange"/>
    </section>
    <table>
      <thead>
        <!-- <tr v-for="data in rawData" :key="data.__EMPTY">
          <template v-for="(element, index) in data">
            <th :key="element.key" v-if="data.__EMPTY == 1">{{element.key}} - {{index}}</th>
          </template>
        </tr> -->
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