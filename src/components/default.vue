<template>
  <div class="home">
    <p>{{ selectedDateLocalString }}</p>
    <label class="label" @click="toggleCalendar">
      <img src="../assets/calendar.png" alt="" />
      <span v-if="selectedDate">{{ selectedDateString }}</span>
      <span v-else>Added days</span>
    </label>
    <div class="date-picker" v-if="showCalendar">
      <div class="header">
        <button @click="prevYear" style="color: #000">&lt;&lt;</button>
        <button v-if="!showYearDropdown && !showMonthDropdown" @click="prevMonth" style="color: #000">&lt;</button>

        <div class="dropdown" @click="showYears">
          {{ currentYear }}
        </div>
        <div class="dropdown" v-if="!showYearDropdown && !showMonthDropdown" @click="showMonths">
          {{ monthNames[currentMonth] }}
        </div>

        <button v-if="!showYearDropdown && !showMonthDropdown" @click="nextMonth" style="color: #000">&gt;</button>
        <button @click="nextYear" style="color: #000">&gt;&gt;</button>
      </div>
      <div class="body">
        <div v-if="showYearDropdown" class="years">
          <div
            v-for="year in yearRange"
            :key="year"
            :class="['year', { selected: year === currentYear }]"
            @click="selectYear(year)"
          >
            {{ year }}
          </div>
        </div>
        <div v-else-if="showMonthDropdown" class="months">
          <div
            v-for="(month, index) in monthNames"
            :key="month"
            :class="['month', { selected: index === currentMonth }]"
            @click="selectMonth(index)"
          >
            {{ month }}
          </div>
        </div>
        <div v-else>
          <div class="weekdays">
            <div v-for="day in weekdays" :key="day" class="weekday">
              {{ day }}
            </div>
          </div>
          <div class="days">
            <div
              v-for="date in calendarDays"
              :key="date.key"
              :class="[
                'day',
                {
                  today: date.isToday,
                  selected: date.isSelected,
                  'not-current': date.notCurrent,
                },
              ]"
              @click="selectDate(date)"
            >
              {{ date.day }}
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from "vue";

const showCalendar = ref(false);
const selectedDate = ref(null);
const currentDate = new Date();
const currentYear = ref(currentDate.getFullYear());
const currentMonth = ref(currentDate.getMonth());

const showMonthDropdown = ref(false);
const showYearDropdown = ref(false);

const monthNames = [
  "Yan", "Fev", "Mar", "Apr", "May", "Iyu", "Iyu", "Avg", "Sen", "Okt", "Noy", "Dek"
];
const weekdays = ["Du", "Se", "Ch", "Pa", "Ju", "Sh", "Ya"];

const toggleCalendar = () => {
  showCalendar.value = !showCalendar.value;
  showYearDropdown.value = false;
  showMonthDropdown.value = false;
};

const showMonths = () => {
  showMonthDropdown.value = true;
  showYearDropdown.value = false;
};

const showYears = () => {
  showYearDropdown.value = true;
  showMonthDropdown.value = false;
};

const isToday = (date) => {
  const today = new Date();
  return (
    date.getDate() === today.getDate() &&
    date.getMonth() === today.getMonth() &&
    date.getFullYear() === today.getFullYear()
  );
};

const isSelectedDate = (date) => {
  return (
    selectedDate.value &&
    date.getDate() === selectedDate.value.getDate() &&
    date.getMonth() === selectedDate.value.getMonth() &&
    date.getFullYear() === selectedDate.value.getFullYear()
  );
};

const selectDate = (date) => {
  selectedDate.value = new Date(date.year, date.month, date.day);
  if (date.notCurrent) {
    currentYear.value = date.year;
    currentMonth.value = date.month;
  }
  showCalendar.value = false;
};

const daysInMonth = (year, month) => {
  return new Date(year, month + 1, 0).getDate();
};

const calendarDays = computed(() => {
  const year = currentYear.value;
  const month = currentMonth.value;

  const firstDayOfMonth = new Date(year, month, 1).getDay();
  const totalDaysInCurrentMonth = daysInMonth(year, month);

  const previousMonth = month === 0 ? 11 : month - 1;
  const previousMonthYear = month === 0 ? year - 1 : year;
  const totalDaysInPreviousMonth = daysInMonth(previousMonthYear, previousMonth);

  const nextMonth = month === 11 ? 0 : month + 1;
  const nextMonthYear = month === 11 ? year + 1 : year;

  let days = [];

  const adjustedFirstDayOfMonth = (firstDayOfMonth + 6) % 7;
  for (let i = adjustedFirstDayOfMonth - 1; i >= 0; i--) {
    days.push({
      key: `prev-${i}`,
      day: totalDaysInPreviousMonth - i,
      month: previousMonth,
      year: previousMonthYear,
      notCurrent: true,
      isToday: false,
      isSelected: false,
    });
  }

  for (let i = 1; i <= totalDaysInCurrentMonth; i++) {
    const date = new Date(year, month, i);
    days.push({
      key: `current-${i}`,
      day: i,
      month: month,
      year: year,
      notCurrent: false,
      isToday: isToday(date),
      isSelected: isSelectedDate(date),
    });
  }

  const remainingDays = 42 - days.length;
  for (let i = 1; i <= remainingDays; i++) {
    days.push({
      key: `next-${i}`,
      day: i,
      month: nextMonth,
      year: nextMonthYear,
      notCurrent: true,
      isToday: false,
      isSelected: false,
    });
  }

  return days;
});

const prevMonth = () => {
  if (currentMonth.value === 0) {
    currentMonth.value = 11;
    currentYear.value--;
  } else {
    currentMonth.value--;
  }
};

const nextMonth = () => {
  if (currentMonth.value === 11) {
    currentMonth.value = 0;
    currentYear.value++;
  } else {
    currentMonth.value++;
  }
};

const prevYear = () => {
  currentYear.value--;
};

const nextYear = () => {
  currentYear.value++;
};

const selectMonth = (index) => {
  currentMonth.value = index;
  showMonthDropdown.value = false;
};

const selectYear = (year) => {
  currentYear.value = year;
  showYearDropdown.value = false;
  showMonthDropdown.value = true;
};

const yearRange = computed(() => {
  const startYear = currentYear.value - 4;
  const endYear = currentYear.value + 5;
  const years = [];
  for (let i = startYear; i <= endYear; i++) {
    years.push(i);
  }
  return years;
});

const selectedDateString = computed(() => {
  return selectedDate.value ? selectedDate.value.toISOString().split('T')[0] : "";
});

const selectedDateLocalString = computed(() => {
  return selectedDate.value ? selectedDate.value.toString() : "";
});
</script>


<style scoped>
.home {
  width: 600px;
  height: 100vh;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}

.label {
  display: flex;
  align-items: center;
  gap: 10px;
  border: 1px solid #002;
  padding: 6px 100px 6px 10px;
  border-radius: 10px;
  cursor: pointer;
}

.date-picker {
  margin-left: 125px;
  margin-top: 20px;
  border: 1px solid #002;
  padding: 30px;
  border-radius: 10px;
  background-color: #fff;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
  width: 300px;
}

.header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding-bottom: 10px;
  
}

.dropdown {
  cursor: pointer;
  padding: 5px;
  border-radius: 5px;
  margin: 0 5px;
}
.dropdown:hover{
  color: #2a56b5;
}

.modal {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  background: rgba(0, 0, 0, 0.5);
}

.modal-content {
  background: #fff;
  padding: 20px;
  border-radius: 10px;
  max-height: 80vh;
  overflow-y: auto;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.2);
}

.weekdays {
  display: flex;
  justify-content: space-around;
  border-bottom: 1px solid #ccc;
}

.weekday {
  width: 24px;
  text-align: center;
  font-weight: bold;
  font-size: 11px;
}

.days {
  display: flex;
  flex-wrap: wrap;
}


.day {
  width: 23.1px;
  display: flex;
  justify-content: center;
  padding: 9px;
  cursor: pointer;
  border-radius: 5px;
  font-size: 12px;
  font-weight: 600;
  border: 1px solid #fff;
}

.day.today {
  border: 1px solid #ccc;
}

.day.selected {
  background-color: #a3bffa;
}

.day.not-current {
  color: #ccc;
  font-family: 400;
}

.day:hover {
  background-color: #eef;
}
.modal{
  background: #fff;
}
.modal-content{
  width: 330px;
  display: flex;
  flex-wrap: wrap;
  gap: 20px;
}
.modal-content>div{
  cursor: pointer;
}

.months{
  display: flex;
  justify-content: center;
  flex-wrap: wrap;
  border-top: 1px solid #ccc;

}
.years{
  display: flex;
  flex-wrap: wrap;
}
.years>div{
  width: 70px;
  display: flex;
  justify-content:center;
  align-items: center;
  padding: 20px 0;
  cursor: pointer;
  font-size: 12px;
}

.months>div{
  width: 70px;
  display: flex;
  justify-content: center;
  padding: 15px 0;
  cursor: pointer;
  font-size: 12px;


}
.months>div:hover, .years>div:hover{
  color: #2a56b5;
}
</style>