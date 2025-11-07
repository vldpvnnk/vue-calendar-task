<template>
  <div class="calendar">
    <div class="calendar-header">
      <button @click="prevMonth" class="nav-arrow">&lt;</button>
      <span class="month-year">{{ formattedMonthYear }}</span>
      <button @click="nextMonth" class="nav-arrow">&gt;</button>
    </div>

    <div class="calendar-weekdays">
      <div v-for="day in weekDays" :key="day" class="weekday">{{ day }}</div>
    </div>

    <div class="calendar-grid">
      <div
        v-for="(day, index) in calendarGrid"
        :key="index"
        class="calendar-day"
        :class="{
          'not-current-month': !day.isCurrentMonth,
          'selected': day.isSelected,
          'today': day.isToday
        }"
        @click="selectDate(day)"
      >
        {{ day.dayNumber }}
      </div>
    </div>
  </div>
</template>

<script>
// Локализация
const locales = {
  en: {
    months: [
      'January', 'February', 'March', 'April', 'May', 'June',
      'July', 'August', 'September', 'October', 'November', 'December'
    ],
    weekdays: ['Sun', 'Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat']
  },
  ru: {
    months: [
      'Январь', 'Февраль', 'Март', 'Апрель', 'Май', 'Июнь',
      'Июль', 'Август', 'Сентябрь', 'Октябрь', 'Ноябрь', 'Декабрь'
    ],
    weekdays: ['Вс', 'Пн', 'Вт', 'Ср', 'Чт', 'Пт', 'Сб']
  }
};

export default {
  name: 'AppCalendar',
  props: {
    initialDate: {
      type: String, 
      default: null
    },
    lang: {
      type: String,
      default: 'en'
    }
  },
  data() {
    return {
      currentDate: new Date(),   
      selectedDate: new Date(),
      locales: locales
    };
  },
  computed: {
    currentLocale() {
      return this.locales[this.lang] || this.locales['en'];
    },
    formattedMonthYear() {
      const month = this.currentLocale.months[this.currentDate.getMonth()];
      const year = this.currentDate.getFullYear();
      return `${month} ${year}`;
    },
    weekDays() {
      return this.currentLocale.weekdays;
    },

    calendarGrid() {
      const year = this.currentDate.getFullYear();
      const month = this.currentDate.getMonth();

      const firstDayOfMonth = new Date(year, month, 1);
      const firstDayOfWeek = firstDayOfMonth.getDay();

      const gridStartDate = new Date(firstDayOfMonth);
      gridStartDate.setDate(1 - firstDayOfWeek);

      const grid = [];
      const todayISO = this.formatDateISO(new Date());
      const selectedISO = this.formatDateISO(this.selectedDate);

      for (let i = 0; i < 42; i++) {
        const date = new Date(gridStartDate);
        const dateISO = this.formatDateISO(date);

        grid.push({
          date: new Date(date),
          dayNumber: date.getDate(),
          isCurrentMonth: date.getMonth() === month,
          isToday: dateISO === todayISO,
          isSelected: dateISO === selectedISO
        });

        gridStartDate.setDate(gridStartDate.getDate() + 1);
      }
      return grid;
    }
  },
  methods: {
    prevMonth() {
      const newDate = new Date(this.currentDate);
      newDate.setMonth(newDate.getMonth() - 1);
      this.currentDate = newDate;
    },
    nextMonth() {
      const newDate = new Date(this.currentDate);
      newDate.setMonth(newDate.getMonth() + 1);
      this.currentDate = newDate;
    },

    selectDate(day) {
      this.selectedDate = day.date;
      
      if (!day.isCurrentMonth) {
        this.currentDate = day.date;
      }
      
      this.$emit('date-selected', this.formatDateISO(day.date));
    },

    formatDateISO(date) {
      const year = date.getFullYear();
      const month = (date.getMonth() + 1).toString().padStart(2, '0');
      const day = date.getDate().toString().padStart(2, '0');
      return `${year}-${month}-${day}`;
    },
    parseISOString(dateString) {
      const parts = dateString.split('-');
      return new Date(parts[0], parts[1] - 1, parts[2]);
    }
  },
  created() {
    let initial = new Date();

    if (this.initialDate) {
      try {
        initial = this.parseISOString(this.initialDate);
      } catch (e) {
        console.error("Invalid initialDate format. Use 'YYYY-MM-DD'.", e);
      }
    }
    
    this.selectedDate = initial;
    this.currentDate = initial;
  }
};
</script>

<style scoped>
.calendar {
  width: 300px;
  border: 1px solid #ddd;
  border-radius: 8px;
  padding: 15px;
  font-family: Arial, sans-serif;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.05);
}

.calendar-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 15px;
}

.month-year {
  font-size: 1.2em;
  font-weight: bold;
}

.nav-arrow {
  background: none;
  border: none;
  font-size: 1.5em;
  cursor: pointer;
  color: #007bff;
}
.nav-arrow:hover {
  color: #0056b3;
}

.calendar-weekdays,
.calendar-grid {
  display: grid;
  grid-template-columns: repeat(7, 1fr);
  text-align: center;
}

.weekday {
  font-weight: bold;
  color: #666;
  padding: 5px 0;
  font-size: 0.9em;
}

.calendar-day {
  padding: 10px 0;
  cursor: pointer;
  border-radius: 4px;
  transition: background-color 0.2s, color 0.2s;
  line-height: 1.2em;
}

.calendar-day:hover {
  background-color: #f0f0f0;
}

.not-current-month {
  color: #ccc;
}
.not-current-month:hover {
  background-color: #f9f9f9;
}

.today {
  border: 1px solid #007bff;
}

.selected {
  background-color: #007bff; 
  color: white;
  border: 1px solid #007bff;
}
.selected:hover {
  background-color: #0056b3;
}
</style>