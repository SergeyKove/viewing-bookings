<template>
  <div class="flex-column time">
    <div :style="{ height: `${cellParams.heightHeaderCell}px` }"></div>
    <div v-for="item in workTime" :style="{ height: `${cellParams.heightCell}px` }">
      {{ item.hour }}:{{ item.minute === 0 ? '00' : item.minute }}
    </div>
  </div>
</template>

<script setup>
const props = defineProps({
  workTime: Array,
  cellParams: Object,
})
</script>

<style scoped>
.time {
  font-family: 'Inter';
  font-weight: 400;
  font-size: 11px;
  line-height: 14px;
  letter-spacing: 0px;
  color: rgba(255, 255, 255, 0.48);
  position: sticky;
  left: 0;
}
</style>
