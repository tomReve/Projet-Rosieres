<template>
  <div class="home">
    <input-xls @change="dataChange"/>
    <p>{{ rawData }}</p>
  </div>
</template>

<script>
import InputXLS from '@/components/InputXLS'

export default {
  name: 'Home',
  components: {
    'input-xls':InputXLS
  },
  data(){
    return {
      rawData:[]
    }
  },
  methods:{
    dataChange(e) {
      this.rawData = e;
    }
  }
}
</script>
