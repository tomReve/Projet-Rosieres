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
              <v-select :options="genderOptions" @input="filterGenderChange"/>
            </th>
            <th>
              <v-select :options="countryOptions" @input="filterCountryChange"/>
            </th>
            <th>
              <input type="number" min="0" v-model="filterAge">
            </th>
            <th>
              <v-date-picker color="blue" v-model="filterDate" :value="filterDate" mode="range"/>
              <button @click="filterDate.start = null; filterDate.end = null">Clear</button>
            </th
            <th>
              <input type="number" min="0" v-model="filterId">
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
  import vSelect from 'vue-select';
  import Calendar from 'v-calendar/lib/components/calendar.umd';
  import DatePicker from 'v-calendar/lib/components/date-picker.umd';

  export default {
    name: 'Home',
    components: {
      'input-xls': InputXLS,
      'pagination': Pagination,
      'v-select':vSelect,
      'v-calendar':Calendar,
      'v-date-picker':DatePicker
    },
    data() {
      return {
        rawData: [],
        elementsPerPageOptions:[10,25,50],
        elementsPerPage:null,
        page:1,
        genderOptions:[],
        countryOptions:[],
        filterFirstName:null,
        filterLastName:null,
        filterGender:null,
        filterCountry:null,
        filterAge:null,
        filterDate:{
          start: null,
          end: null,
        },
        filterId:null
      }
    },
    computed:{
      filtredRawData(){
        let data = this.rawData;
        if(this.filterFirstName) {
          data = data.filter(element => (element['First Name'].toLowerCase().includes(this.filterFirstName.toLowerCase())));
        }
        if(this.filterLastName) {
          data = data.filter(element => (element['Last Name'].toLowerCase().includes(this.filterLastName.toLowerCase())));
        }
        if(this.filterGender) {
          data = data.filter(element => (element['Gender'].toLowerCase() == this.filterGender.toLowerCase()));
        }
        if(this.filterCountry) {
          data = data.filter(element => (element['Country'].toLowerCase() == this.filterCountry.toLowerCase()));
        }
        if(this.filterAge) {
          data = data.filter(element => (element['Age'] == this.filterAge));
        }
        if(this.filterDate.start && this.filterDate.end) {
          const filteredData = [];
          data.forEach(element => {
            const date = element['Date'].split('/');
            const correctDate = Date.parse(new Date(date[2],date[1],date[0]));
            if(correctDate >= Date.parse(this.filterDate.start) && correctDate <= Date.parse(this.filterDate.end)) {
              filteredData.push(element);
            }
          });
          data = filteredData;
        }
        if(this.filterId) {
          data = data.filter(element => (element['Id'].toString().includes(this.filterId)));
        }

        return data;
      },
      pageRawData(){
        const firstElement = this.page*this.elementsPerPage - this.elementsPerPage;
        const lastElement = this.page*this.elementsPerPage;
        return this.filtredRawData.slice(firstElement,lastElement);
      },
      numberOfPages(){
        return this.filtredRawData.length/this.elementsPerPage;
      }
    },
    mounted(){
      // ? set number of elements per page
      this.elementsPerPage = this.elementsPerPageOptions[0];
      // ? Check if they are data in the localStorage and use it if
      if(localStorage.rawData) {
        this.rawData = JSON.parse(localStorage.rawData);
      }
      // ? Check types of gender and of countrys
      this.getGenderOptions();
      this.getCountryOptions();
    },
    methods: {
      dataChange(e) {
        this.rawData = e;
        localStorage.rawData = JSON.stringify(this.rawData);
        this.getGenderOptions();
        this.getCountryOptions();
      },
      elementsPerPageChange(e){
        this.elementsPerPage = e;
        this.page = 1;
      },
      paginationChange(e){
        this.page = e;
      },
      filterGenderChange(e){
        this.filterGender = e;
      },
      filterCountryChange(e){
        this.filterCountry = e;
      },
      filterDateChange(e){
        this.filterDate.start = e.start;
        this.filterDate.end = e.end;
      },
      getGenderOptions(){
        let genders = [];
        this.rawData.forEach(element => {
          const newGenders = genders.filter(gender => (gender == element['Gender']));
          if(newGenders.length == 0) {
            genders.push(element['Gender']);
          }
        });
        this.genderOptions = genders;
      },
      getCountryOptions(){
        let countrys = [];
        this.rawData.forEach(element => {
          const newcountrys = countrys.filter(gender => (gender == element['Country']));
          if(newcountrys.length == 0) {
            countrys.push(element['Country']);
          }
        });
        this.countryOptions = countrys;
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