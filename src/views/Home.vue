<template>
  <div class="home">
    <img class="header" alt="Vue logo" src="./../assets/header-bannier.jpg">
    <section id="pagination">
      <div class="limite">
        <p>Afficher</p>
        <v-select :options="elementsPerPageOptions" :value="elementsPerPage" :searchable="false"
          @input="elementsPerPageChange" />
        <p>personnes par page</p>
      </div>
      <input-xls @change="dataChange" />
      <pagination v-if="rawData.length > 0" :page="page" :pages="numberOfPages" @change="paginationChange" />
    </section>
    <table v-if="rawData.length > 0">
      <thead v-for="data in rawData" :key="data.__EMPTY">
        <tr v-if="data.__EMPTY == 1">
          <th>Prénom</th>
          <th>Nom</th>
          <th>Genre</th>
          <th>Pays</th>
          <th>Âge</th>
          <th>Date</th>
          <th>Id</th>
        </tr>
        <tr class="trRowInput" :key="data.__EMPTY" v-if="data.__EMPTY == 1">
          <th>
            <img src="./../assets/search-solid.svg">
            <input type="text" v-model="filterFirstName">
          </th>
          <th>
            <img src="./../assets/search-solid.svg">
            <input type="text" v-model="filterLastName">
          </th>
          <th>
            <v-select :options="genderOptions" :searchable="false" @input="filterGenderChange" />
          </th>
          <th>
            <v-select :options="countryOptions" @input="filterCountryChange" />
          </th>
          <th>
            <img src="./../assets/search-solid.svg">
            <input type="number" min="0" v-model="filterAge">
          </th>
          <th>
            <v-date-picker color="blue" v-model="filterDate" :value="filterDate" mode="range" />
            <button @click="filterDate.start = null; filterDate.end = null">Clear</button>
          </th>
          <th>
            <img src="./../assets/search-solid.svg">
            <input type="number" min="0" v-model="filterId">
          </th>
        </tr>
      </thead>
      <tbody v-if="pageRawData.length >= 1">
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
      <tbody v-else>
        <tr>
          <td>
            Aucun élément ne correspond à votre recherche.
            <button @click="clearFilters">Vider les filtres</button>
          </td>
        </tr>
      </tbody>
    </table>
    <table v-else>
      <tr>
        <td>Importez des données pour commencer à travailler.</td>
      </tr>
    </table>
     <section id="pagination-bottom">
      <pagination :page="page" :pages="numberOfPages" @change="paginationChange" />
    </section>
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
      'v-select': vSelect,
      'v-calendar': Calendar,
      'v-date-picker': DatePicker

    },
    data() {
      return {
        rawData: [],
        elementsPerPageOptions: [10, 25, 50],
        elementsPerPage: null,
        page: 1,
        genderOptions: [],
        countryOptions: [],
        filterFirstName: null,
        filterLastName: null,
        filterGender: null,
        filterCountry: null,
        filterAge: null,
        filterDate: {
          start: null,
          end: null,
        },
        filterId: null
      }
    },
    computed: {
      filtredRawData() {
        let data = this.rawData;
        if (this.filterFirstName) {
          data = data.filter(element => (element['First Name'].toLowerCase().includes(this.filterFirstName
            .toLowerCase())));
        }
        if (this.filterLastName) {
          data = data.filter(element => (element['Last Name'].toLowerCase().includes(this.filterLastName
            .toLowerCase())));
        }
        if (this.filterGender) {
          data = data.filter(element => (element['Gender'].toLowerCase() == this.filterGender.toLowerCase()));
        }
        if (this.filterCountry) {
          data = data.filter(element => (element['Country'].toLowerCase() == this.filterCountry.toLowerCase()));
        }
        if (this.filterAge) {
          data = data.filter(element => (element['Age'] == this.filterAge));
        }
        if (this.filterDate.start && this.filterDate.end) {
          const filteredData = [];
          data.forEach(element => {
            const date = element['Date'].split('/');
            const correctDate = Date.parse(new Date(date[2], date[1], date[0]));
            if (correctDate >= Date.parse(this.filterDate.start) && correctDate <= Date.parse(this.filterDate
                .end)) {
              filteredData.push(element);
            }
          });
          data = filteredData;
        }
        if (this.filterId) {
          data = data.filter(element => (element['Id'].toString().includes(this.filterId)));
        }

        return data;
      },
      pageRawData() {
        const firstElement = this.page * this.elementsPerPage - this.elementsPerPage;
        const lastElement = this.page * this.elementsPerPage;
        return this.filtredRawData.slice(firstElement, lastElement);
      },
      numberOfPages() {
        return this.filtredRawData.length / this.elementsPerPage;
      }
    },
    mounted() {
      // ? set number of elements per page
      this.elementsPerPage = this.elementsPerPageOptions[0];
      // ? Check if they are data in the localStorage and use it if
      if (localStorage.rawData) {
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
      elementsPerPageChange(e) {
        this.elementsPerPage = e;
        this.page = 1;
      },
      paginationChange(e) {
        this.page = e;
      },
      filterGenderChange(e) {
        this.filterGender = e;
      },
      filterCountryChange(e) {
        this.filterCountry = e;
      },
      filterDateChange(e) {
        this.filterDate.start = e.start;
        this.filterDate.end = e.end;
      },
      getGenderOptions() {
        let genders = [];
        this.rawData.forEach(element => {
          const newGenders = genders.filter(gender => (gender == element['Gender']));
          if (newGenders.length == 0) {
            genders.push(element['Gender']);
          }
        });
        this.genderOptions = genders;
      },
      getCountryOptions() {
        let countrys = [];
        this.rawData.forEach(element => {
          const newcountrys = countrys.filter(gender => (gender == element['Country']));
          if (newcountrys.length == 0) {
            countrys.push(element['Country']);
          }
        });
        this.countryOptions = countrys;
      },
      clearFilters(){
        this.filterFirstName = null;
        this.filterLastName = null;
        this.filterGender = null;
        this.filterCountry = null;
        this.filterAge = null;
        this.filterDate = {
          start:null,
          end:null
        };
        this.filterId = null;
      }
    }
  }
</script>

<style lang="scss">
  @import "vue-select/src/scss/vue-select.scss";

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

  .home #pagination {
    display: flex;
    justify-content: space-between;
    margin: 4vh 0 4vh 0;
  }

  .home #pagination div.limite,
  .home #pagination div.pagination,
  .home #pagination div.btn-file {
    display: flex;
    flex-direction: row;
    max-width: 30%;
    width: 100%;
    justify-content: center;
    align-items: center;
  }

  .home #pagination div.limite .v-select {
    width: 25%;
    margin: 0px 20px 0px 20px;
    background-color: #ffff8a;
    font-weight: 700;
    border-radius: 100vh;
  }

  .home #pagination .limite svg {
    fill: #000;
  }

  .home #pagination .vs__selected {
    height: 100%;
  }

  .home #pagination .vs__dropdown-toggle {
    border: none;
  }

  .home #pagination div.btn-file {
    width: auto;
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

  .home thead tr {
    margin-bottom: 1vh;
  }

  .home thead tr.trRowInput th {
    display: flex;
    width: 100%;
    height: calc(100% - 1.5vh);
    margin: 0.75vh;
    background-color: #FFFFFF;
    margin-right: 0;
  }

  .home thead tr.trRowInput th:last-child {
    margin-right: 0.75vh;
  }

  .home thead tr.trRowInput th img {
    padding-right: 10px;
  }

  .home thead tr.trRowInput input {
    outline: none;
    width: 20%;
    background-color: rgba(0, 0, 0, 0);
    border: none;
    border-bottom: solid 2px #ffe799;
    transition: 0.3s;
  }

  .home thead tr.trRowInput input:focus {
    width: 100%;
    background-color: rgba(0, 0, 0, 0);
    border: none;
    border-bottom: solid 2px #FFC400;
  }

  .home thead th {
    display: flex;
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

  .home thead th,
  .home tbody td {
    width: 100%;
    height: 50%;
  }
</style>