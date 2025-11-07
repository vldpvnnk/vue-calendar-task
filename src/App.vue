<template>
  <div id="app">
    <div class="example">
      <h3>Пример 1: По умолчанию (Английский)</h3>
      <app-calendar @date-selected="handleDateSelected" /> <p>Выбранная дата: {{ selectedDate1 }}</p>
    </div>

    <div class="example">
      <h3>Пример 2: initialDate="2025-01-15" и lang="ru"</h3>
      <app-calendar initialDate="2025-01-15"
        lang="ru"
        @date-selected="handleDateSelected2"
      />
      <p>Выбранная дата: {{ selectedDate2 }}</p>
    </div>
  </div>
</template>

<script>
import AppCalendar from './components/Calendar.vue'

export default {
  name: 'App',
  components: {
    AppCalendar
  },
  data() {
    return {
      selectedDate1: 'Пока не выбрана',
      selectedDate2: 'Пока не выбрана',
    }
  },
  methods: {
    handleDateSelected(date) {
      this.selectedDate1 = date
    },
    handleDateSelected2(date) {
      this.selectedDate2 = date
    }
  }
}
</script>

<style>
body {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  color: #2c3e50;
  margin-top: 60px;
  display: flex;
  justify-content: center;
  gap: 40px;
}
#app {
  display: flex;
  gap: 40px;
}
.example {
  display: flex;
  flex-direction: column;
  align-items: center;
}
</style>