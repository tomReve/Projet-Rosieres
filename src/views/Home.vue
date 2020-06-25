<template>
  <div class="home">
    <input-xls @change="dataChange" />
    <table>
      <thead>
        <!-- <tr v-for="data in rawData" :key="data.__EMPTY">
          <template v-for="(element, index) in data">
            <th :key="element.key" v-if="data.__EMPTY == 1">{{element.key}} - {{index}}</th>
          </template>
        </tr> -->
      </thead>
      <tbody>
        <tr v-for="data in rawData" :key="data.__EMPTY">
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
    <p>{{ rawData }}</p>
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