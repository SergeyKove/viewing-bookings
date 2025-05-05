<template>
  <div class="flex-column header-cell" :style="{ width: `${width}px`, height: `${height}px` }">
    <span> #{{ item.number }} {{ item.capacity + ' чел' }}</span>
    <span>{{ item.zone }}</span>
  </div>
</template>

<script setup>
const props = defineProps({
  item: Object,
  width: [String, Number],
  height: [String, Number],
})
</script>

<style scoped></style>
