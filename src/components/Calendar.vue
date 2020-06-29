<template>
  <div>
    <template v-for="year in Object.keys(groupedRange)">
      <section class="month" v-for="month in Object.keys(groupedRange[year])" :key="month">
        <h1 class="title">{{ formatDateToMonth(month) }} {{ formatDateToYear(year) }}</h1>
        <table>
          <tr>
            <th></th>
            <th v-for="dayInWeek in daysInWeek" :key="dayInWeek">{{dayInWeek}}</th>
          </tr>
          <tr v-for="week in Object.keys(groupedRange[year][month])" :key="week">
            <th>{{ week }}</th>
            <td
              v-for="dayIndex in dayIndexesInWeek"
              :key="dayIndex"
            >{{ formatDateToDay(groupedRange[year][month][week].find(date => getDay(date) === dayIndex)) }}</td>
          </tr>
        </table>
      </section>
    </template>
  </div>
</template>

<script>
import {
  eachDayOfInterval,
  format,
  startOfMonth,
  startOfYear,
  formatISO,
  getISOWeek,
  getDay
} from "date-fns";

import { groupBy, defaultsDeep } from "lodash-es";

export default {
  name: "Calendar",
  props: ["range"],
  methods: {
    getDay,
    formatDate: (value, type) =>
      format(value, type === "long" ? "d MMM yyyy" : "yyyy-MM-dd"),
    formatDateToMonth: value => new Intl.DateTimeFormat('nl-BE', { month: "long" }).format(new Date(value)),
    formatDateToYear: value => new Intl.DateTimeFormat('nl-BE', { year: "numeric" }).format(new Date(value)),
    formatDateToDay: value => (value ? new Intl.DateTimeFormat('nl-BE', { day: "numeric" }).format(new Date(value)) : null)
  },
  computed: {
    groupedRange() {
      if (!this.range) return [];

      return eachDayOfInterval(this.range).reduce((acc, date) => {
        const year = formatISO(startOfYear(date));
        const month = formatISO(startOfMonth(date));
        const isoWeek = getISOWeek(date);

        const initalArray = acc?.[year]?.[month]?.[isoWeek];

        return defaultsDeep(acc, {
          [year]: {
            [month]: {
              [isoWeek]: [...(initalArray || []), date]
            }
          }
        });
      }, {});
    },
    daysInWeek() {
      return [
        "Ma.", //ndag",
        "Din.", //sdag",
        "Woe.", //nsdag",
        "Don.", //derdag",
        "Vrij.", //jdag",
        "Zat.", //erdag",
        "Zon." //dag",
      ];
    },

    dayIndexesInWeek() {
      return [1, 2, 3, 4, 5, 6, 0];
    }
  }
};
</script>

<style scoped>
table {
  border-collapse: collapse;
  table-layout: fixed;
}

th,
td {
  border: 1px solid #bbb;
  padding: 0.5rem;
  width: 12.5%;
  text-align: center;
  height: 2rem;
}

pre {
  max-height: 21rem;
  border: 1px solid #ddd;
  overflow: scroll;
  padding: 0.75em;
}

.month {
  margin-bottom: 3rem;
  break-inside: avoid;
}
</style>
