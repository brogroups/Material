<script setup>
import Calendar from '../components/calendar.vue'
import Multiselect from '@/components/multiselect.vue';
</script>

<template>
  <div class="header">
    <Calendar write-day="2021-0"/>
    <Multiselect  />
  </div>
</template>

<style scoped>
.header {
  width: 100%;
  display: flex;
  align-items: center;
  justify-content: space-around;
}
</style>
