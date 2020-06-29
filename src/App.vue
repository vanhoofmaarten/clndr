<template>
  <div>
    <DateRangeForm @submit="handleDateRange" class="date-range-form" />
    <Calendar :range="dateRange" class="calendar" />
  </div>
</template>

<script>
import Calendar from "./components/Calendar.vue";
import DateRangeForm from "./components/DateRangeForm.vue";

export default {
  name: "App",
  components: {
    Calendar,
    DateRangeForm
  },
  data: () => ({
    dateRange: null
  }),
  methods: {
    handleDateRange(value) {
      this.dateRange = value;
    }
  }
};
</script>

<style scoped>
.date-range-form,
.calendar {
  padding: 3rem;
}

.date-range-form {
  border-bottom: 1px solid #bbb;
}

@media print {
	.date-range-form {
    display: none;
  }
}
</style>
