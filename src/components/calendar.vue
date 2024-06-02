<template>
  <div class="date-pic-temp">
    <div class="unique-label" @click="toggleDatePicker">
      <span>{{ selectedDateText }}</span>
    </div>
    <div v-if="isDatePickerVisible" class="unique-date-picker" @click.stop>
      <div class="unique-header">
        <span class="unique-double-dropdown" @click="prevYear">&laquo;</span>
        <span v-if="currentMode === 'date'" class="unique-dropdown" @click="prev">&lt;</span>
        <span v-if="currentMode !== 'month'" @click="showYears">{{ currentYear }}</span>
        <span v-if="currentMode !== 'year'" @click="showMonths">{{ currentMonthName }}</span>
        <span v-if="currentMode === 'date'" class="unique-dropdown" @click="next">&gt;</span>
        <span class="unique-double-dropdown" @click="nextYear">&raquo;</span>
      </div>
      <div class="unique-body">
        <template v-if="currentMode === 'date'">
          <div class="unique-weekdays">
            <div class="unique-weekday" v-for="weekday in weekdays" :key="weekday">{{ weekday }}</div>
          </div>
          <div class="unique-days">
            <div
              v-for="day in days"
              :key="day.date"
              :class="day.classes"
              @click="selectDate(day)"
            >
              {{ day.text }}
            </div>
          </div>
        </template>
        <template v-else-if="currentMode === 'year'">
          <div class="unique-years">
            <div
              v-for="year in years"
              :key="year"
              class="unique-year"
              @click="selectYear(year)"
            >
              {{ year }}
            </div>
          </div>
        </template>
        <template v-else-if="currentMode === 'month'">
          <div class="unique-months">
            <div
              v-for="(month, index) in months"
              :key="month"
              class="unique-month"
              @click="selectMonth(index)"
            >
              {{ month }}
            </div>
          </div>
        </template>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    writeDay: {
      type: String,
      default: ''
    }
  },
  data() {
    return {
      isDatePickerVisible: false,
      selectedDate: new Date(),
      currentYear: new Date().getFullYear(),
      currentMonth: new Date().getMonth(),
      currentMode: 'date',
      weekdays: ['Dush', 'Sesh', 'Chor', 'Pay', 'Jum', 'Shan', 'Yak'],
      months: [
        'Yanvar', 'Fevral', 'Mart', 'Aprel', 'May', 'Iyun',
        'Iyul', 'Avgust', 'Sentabr', 'Oktabr', 'Noyabr', 'Dekabr'
      ]
    };
  },
  computed: {
    selectedDateText() {
      return this.formattedSelectedDate;
    },
    formattedSelectedDate() {
      return this.selectedDate.toISOString().substring(0, 10);
    },
    currentMonthName() {
      return this.months[this.currentMonth];
    },
    days() {
      const firstDay = new Date(this.currentYear, this.currentMonth, 1).getDay();
      const daysInMonth = new Date(this.currentYear, this.currentMonth + 1, 0).getDate();
      const days = [];
      let startDay = firstDay === 0 ? 6 : firstDay - 1; // Adjusting for Monday start

      // Previous month's last days or current month's first days
      if (startDay > 0) {
        const prevMonthDays = new Date(this.currentYear, this.currentMonth, 0).getDate();
        for (let i = startDay; i > 0; i--) {
          days.push({ 
            text: prevMonthDays - i + 1, 
            classes: 'unique-day unique-not-current', 
            date: new Date(this.currentYear, this.currentMonth - 1, prevMonthDays - i + 1),
            isCurrentMonth: false 
          });
        }
      }

      // Current month's days
      for (let i = 1; i <= daysInMonth; i++) {
        let dayClass = 'unique-day';
        if (
          i === this.selectedDate.getDate() &&
          this.currentYear === this.selectedDate.getFullYear() &&
          this.currentMonth === this.selectedDate.getMonth()
        ) {
          dayClass += ' unique-selected';
        }
        if (
          i === new Date().getDate() &&
          this.currentYear === new Date().getFullYear() &&
          this.currentMonth === new Date().getMonth()
        ) {
          dayClass += ' unique-today';
        }
        days.push({ 
          text: i, 
          classes: dayClass, 
          date: new Date(this.currentYear, this.currentMonth, i),
          isCurrentMonth: true 
        });
      }

      // Next month's first days
      const nextMonthDays = 42 - days.length;
      for (let i = 1; i <= nextMonthDays; i++) {
        days.push({ 
          text: i, 
          classes: 'unique-day unique-not-current', 
          date: new Date(this.currentYear, this.currentMonth + 1, i),
          isCurrentMonth: false 
        });
      }

      return days;
    },
    years() {
      const years = [];
      for (let i = this.currentYear - 4; i <= this.currentYear + 5; i++) {
        years.push(i);
      }
      return years;
    }
  },
  watch: {
    writeDay: {
      immediate: true,
      handler(newValue) {
        if (newValue) {
          const date = new Date(newValue);
          if (!isNaN(date)) {
            this.selectedDate = date;
            this.currentYear = date.getFullYear();
            this.currentMonth = date.getMonth();
          }
        }
      }
    }
  },
  methods: {
    toggleDatePicker() {
      this.isDatePickerVisible = !this.isDatePickerVisible;
    },
    prev() {
      if (this.currentMode === 'date') {
        this.currentMonth--;
        if (this.currentMonth < 0) {
          this.currentMonth = 11;
          this.currentYear--;
        }
      }
    },
    next() {
      if (this.currentMode === 'date') {
        this.currentMonth++;
        if (this.currentMonth > 11) {
          this.currentMonth = 0;
          this.currentYear++;
        }
      }
    },
    prevYear() {
      this.currentYear--;
    },
    nextYear() {
      this.currentYear++;
    },
    showYears() {
      this.currentMode = 'year';
    },
    showMonths() {
      this.currentMode = 'month';
    },
    selectYear(year) {
      this.currentYear = year;
      this.currentMode = 'month';
    },
    selectMonth(month) {
      this.currentMonth = month;
      this.currentMode = 'date';
    },
    selectDate(day) {
      this.selectedDate = day.date;
      if (!day.isCurrentMonth) {
        this.currentMonth = day.date.getMonth();
        this.currentYear = day.date.getFullYear();
      }
      this.isDatePickerVisible = false;
    },
    closeDatePicker(event) {
      if (!this.$el.contains(event.target)) {
        this.isDatePickerVisible = false;
      }
    }
  },
  mounted() {
    document.addEventListener('click', this.closeDatePicker);
  },
  beforeDestroy() {
    document.removeEventListener('click', this.closeDatePicker);
  }
};
</script>

<style scoped>
/* Add your CSS styles here */
</style>
