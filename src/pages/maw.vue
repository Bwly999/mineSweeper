<script lang="ts" setup>
</script>
<template>
  <div flex flex-col items-center>
    <Card v-for="i in 5" :key="i" :name="''+i" m1 />
  </div>
</template>
