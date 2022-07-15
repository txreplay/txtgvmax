<template>
  <TableData :data="data" />
</template>

<script>
import axios from 'axios'
import TableData from './components/TableData'

export default {
  name: 'App',
  components: {
    TableData
  },
  data() {
    return {
      baseUrl: 'https://ressources.data.sncf.com/api/records/1.0/search/?dataset=tgvmax&q=&sort=date',
      paris: 'PARIS+(intramuros)',
      mtpStRoch: 'MONTPELLIER+SUD+DE+FRANCE',
      mtpSdF: 'MONTPELLIER+SAINT+ROCH',
      data: []
    }
  },
  async created() {
    const resGoing = await axios.get(`${this.baseUrl}&refine.origine=${this.mtpStRoch}&refine.destination=${this.paris}`)
    const resGoingBis = await axios.get(`${this.baseUrl}&refine.origine=${this.mtpSdF}&refine.destination=${this.paris}`)
    const resComing = await axios.get(`${this.baseUrl}&refine.origine=${this.paris}&refine.destination=${this.mtpStRoch}`)
    const resComingBis = await axios.get(`${this.baseUrl}&refine.origine=${this.paris}&refine.destination=${this.mtpSdF}`)
    // console.log(resGoing.data.records.map(data => data.fields))
    // console.log(resGoingBis.data.records.map(data => data.fields))
    // console.log(resComing.data.records.map(data => data.fields))
    // console.log(resComingBis.data.records.map(data => data.fields))
    this.data = [...resGoing.data.records.map(data => data.fields), ...resGoingBis.data.records.map(data => data.fields), ...resComing.data.records.map(data => data.fields), ...resComingBis.data.records.map(data => data.fields)]
  },
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 20px;
}
</style>
