<template>
  <div>
    <form @submit.prevent.stop="setDateRange">
      <div v-for="period in Object.keys(range)" :key="period" class="period">
        <label for="rangeMonth">Kies {{period === "end" ? "eind" : 'start'}}maand:</label>
        <select :id="`rangeMonth${period}`" v-model="range[period].month">
          <option v-for="(month, index) in months" :key="month" :value="index">{{month}}</option>
        </select>
        <select v-model="range[period].year">
          <option v-for="year in years" :key="year" :value="year">{{year}}</option>
        </select>
      </div>
      <button type="submit">Maak tabel</button>
    </form>
    <div v-if="error" class="error">{{error}}</div>
  </div>
</template>

<script>
import {
  eachMonthOfInterval,
  setDayOfYear,
  format,
  addYears,
  eachYearOfInterval,
  lastDayOfMonth,
  parse,
  compareAsc
} from "date-fns";

export default {
  name: "DateRangeForm",
  data: () => ({
    error: null,
    range: {
      start: {
        month: 0,
        year: format(new Date(), "yyyy")
      },
      end: {
        month: 0,
        year: format(new Date(), "yyyy")
      }
    }
  }),
  computed: {
    months() {
      return eachMonthOfInterval({
        start: setDayOfYear(new Date(), 1),
        end: setDayOfYear(new Date(), 365)
      }).map(date =>
        new Intl.DateTimeFormat("nl-BE", { month: "long" }).format(date)
      );
    },
    years() {
      return eachYearOfInterval({
        start: new Date(),
        end: addYears(new Date(), 10)
      }).map(date =>
        new Intl.DateTimeFormat("nl-BE", { year: "numeric" }).format(date)
      );
    }
  },
  methods: {
    setDateRange() {
      this.error = null;
      this.$emit("submit", null)

      const {
        range: { start, end }
      } = this;

      const parseDate = ({ year, month }) => new Date(year, month);
      const startDate = parseDate(start);
      const endDate = lastDayOfMonth(parseDate(end));

      if (compareAsc(startDate, endDate) === 1) {
        this.error = "De startmaand moet vóór de eindmaand vallen.";
      } else {
        this.$emit("submit", {
          start: startDate,
          end: endDate
        });
      }
    }
  }
};
</script>

<style scoped>
form {
  display: flex;
  gap: 3rem;
}

.period {
  display: flex;
  gap: 0.5rem;
}

.error {
  color: red;
  margin-top: 1.5rem;
}
</style>
