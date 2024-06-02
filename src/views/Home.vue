<script setup>
import Calendar from '../components/calendar.vue'
import Multiselect from '@/components/multiselect.vue';
</script>

<template>
  <div class="header">
    <Calendar value="2021-09-25"/>
    <Multiselect multiselect="false"/>
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
