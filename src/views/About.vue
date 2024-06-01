<template>
    <div class="main">
      <label class="label">
        <img src="../assets/calendar.png" alt="" />
        <span></span>
        <span>Tug'ulgan kuningizni tanlang</span>
      </label>
      <div class="date-picker">
        <div class="header">
          <button @click="goToPreviousYear">&lt;&lt;</button>
          <button @click="goToPreviousMonth">&lt;</button>
          <select v-model="currentMonth">
            <option
              v-for="(month, index) in MonthNames"
              :value="index"
              :key="month"
            >
              {{ month }}
            </option>
          </select>
          <select v-model="currentYear">
            <option v-for="year in yearsRange" :key="year" :value="year">
              {{ year }}
            </option>
          </select>
          <button @click="goToNextMonth">&gt;</button>
          <button @click="goToNextYear">&gt;&gt;</button>
        </div>
      </div>
    </div>
  </template>
  
  <script>
  import { ref, computed } from "vue";
  
  export default {
    setup() {
      const showCalendar = ref(false);
      const selectedDate = ref(null);
      const currentDate = new Date();
      const currentYear = ref(currentDate.getFullYear());
      const currentMonth = ref(currentDate.getMonth());
  
      const MonthNames = [
        "Yanvar","Fevral","Mart","Aprel","May","Iyun","Iyul","Avgust","Sentyabr","Oktyabr","Noyabr","Dekabr",
      ];
  
      const yearsRange = computed(() => {
        const startYear = 1990;
        const endYear = new Date().getFullYear() + 50; 
        let years = [];
        for (let year = startYear; year <= endYear; year++) {
          years.push(year);
        }
        return years;
      });
  
      const goToPreviousMonth = () => {
        if (currentMonth.value === 0) {
          currentMonth.value = 11;
          currentYear.value--;
        } else {
          currentMonth.value--;
        }
      };
  
      const goToNextMonth = () => {
        if (currentMonth.value === 11) {
          currentMonth.value = 0;
          currentYear.value++;
        } else {
          currentMonth.value++;
        }
      };
  
      const goToPreviousYear = () => {
        currentYear.value--;
      };
  
      const goToNextYear = () => {
        currentYear.value++;
      };
  
      return {
        showCalendar,
        selectedDate,
        currentMonth,
        currentYear,
        MonthNames,
        yearsRange,
        goToPreviousMonth,
        goToNextMonth,
        goToPreviousYear,
        goToNextYear,
      };
    },
  };
  </script>
  