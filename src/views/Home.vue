<template>
  <div class="home">
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