<script setup>
  import CreateFile from '../components/test.vue'
</script>

<template>
  <div class="header">
   <CreateFile  limit="100" fileType=".json"/>
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
