<template>
<div class="tables" v-if="filteredData.length > 0">
  <table>
    <thead>
      <tr>
        <th>Type</th>
        <th>Départ</th>
        <th>Arrivée</th>
        <th>Arrivée</th>
        <th>Origine</th>
        <th>Destination</th>
      </tr>
    </thead>
    <tbody>
      <tr v-for="(row, key) of filteredData.filter(data => (data.origine === '☀ St Roch' || data.origine === '☀ Sud de France' ))" :key="key" class="table__row" :class="tableRowClassName(row)">
        <td class="cell">
          <span v-if="row.destination === '🗼 Paris'">Aller</span>
          <span v-else>Retour</span>
        </td>
        <td class="cell">{{ formatDate(row.date) }}</td>
        <td class="cell">{{ row.heure_depart }}</td>
        <td class="cell">{{ row.heure_arrivee }}</td>
        <td class="cell">{{ row.origine }}</td>
        <td class="cell">{{ row.destination }}</td>
      </tr>
    </tbody>
  </table>
  <table>
    <thead>
      <tr>
        <th>Type</th>
        <th>Départ</th>
        <th>Arrivée</th>
        <th>Arrivée</th>
        <th>Origine</th>
        <th>Destination</th>
      </tr>
    </thead>
    <tbody>
      <tr v-for="(row, key) of filteredData.filter(data => data.origine === '🗼 Paris')" :key="key" class="table__row" :class="tableRowClassName(row)">
        <td class="cell">
          <span v-if="row.destination === '🗼 Paris'">Aller</span>
          <span v-else>Retour</span>
        </td>
        <td class="cell">{{ formatDate(row.date) }}</td>
        <td class="cell">{{ row.heure_depart }}</td>
        <td class="cell">{{ row.heure_arrivee }}</td>
        <td class="cell">{{ row.origine }}</td>
        <td class="cell">{{ row.destination }}</td>
      </tr>
    </tbody>
  </table>
</div>
<div v-else>
  <img src="./loading.gif"/>
</div>
</template>

<script>

export default {
  name: 'DataTable',
  props: {
    data: {
      type: Array,
      required: true
    },
  },
  data() {
    return {
      nextDayTrips: []
    }
  },
  computed: {
    filteredData() {
      return this.data.map(data => {
        if (data.origine === 'PARIS (intramuros)') { data.origine = '🗼 Paris'; }
        if (data.destination === 'PARIS (intramuros)') { data.destination = '🗼 Paris'; }
        if (data.origine === 'MONTPELLIER SAINT ROCH') { data.origine = '☀ St Roch'; }
        if (data.destination === 'MONTPELLIER SAINT ROCH') { data.destination = '☀ St Roch'; }
        if (data.origine === 'MONTPELLIER SUD DE FRANCE') { data.origine = '☀ Sud de France'; }
        if (data.destination === 'MONTPELLIER SUD DE FRANCE') { data.destination = '☀ Sud de France'; }
        return data
      }).sort(function(a,b) {
        a = a.date.split('-').reverse().join('');
        b = b.date.split('-').reverse().join('');
        return a > b ? 1 : a < b ? -1 : 0;
      })
    },
  },
  methods: {
    tableRowClassName(row) {
      if (row.destination === '🗼 Paris') {
        const nextDay = this.addNextDay(row.date)
        if (nextDay.length > 0) {
          return 'highlight going'
        } else {
          return 'going'
        }
      }
      if (row.origine === '🗼 Paris') {
        if (this.nextDayTrips.includes(row)) {
          return 'highlight coming'
        } else {
          return 'coming'
        }
      }
    },
    isMorning(data) {
      const dayOfWeek = new Date(data.date)
      const hour = data.heure_depart.split(':')[0]

      return (dayOfWeek.getDay() >= 1 && dayOfWeek.getDay() <= 4) && (hour >=7 && hour <= 10)
    },
    isEvening(data) {
      const dayOfWeek = new Date(data.date)
      const hour = data.heure_depart.split(':')[0]

      return (dayOfWeek.getDay() >= 2 && dayOfWeek.getDay() <= 5) && (hour >=15 && hour <= 21)
    },
    addNextDay(date) {
      const nextDayDate = new Date(date)
      nextDayDate.setDate(nextDayDate.getDate() + 1);
      const nextDayFormatted = nextDayDate.getFullYear() + '-' + ('0' + (nextDayDate.getMonth() + 1)).slice(-2) + '-' + ('0' + nextDayDate.getDate()).slice(-2);

      const nextDayTrips = this.filteredData.filter(data => (data.date === nextDayFormatted && this.isEvening(data) && data.origine === '🗼 Paris'))

      nextDayTrips.forEach(data => {
        if (!this.nextDayTrips.includes(data)) {
          this.nextDayTrips.push(data)
        }
      })

      return nextDayTrips
    },
    formatDate(dateString) {
      const date = new Date(dateString);
      return new Intl.DateTimeFormat('default', {weekday: 'long', year: 'numeric', month: 'long', day: 'numeric'}).format(date);
    }
  },
}
</script>

<style>
.tables {
  display: flex;
}
table {
  margin: 0 auto;
}

.table__row.going {
  background-color: #bbf7ff;
  opacity: 0.5;
}
.table__row.coming {
  background-color: #ffdfb0;
  opacity: 0.5;
}

.table__row.highlight {
  font-weight: bold;
  opacity: 1;
  color: #ae0000;
}

.table__row.highlight.going { background-color: #87f1ff; }
.table__row.highlight.coming { background-color: #ffdaa2; }

.cell {
  padding: 2px 25px;
}

</style>
