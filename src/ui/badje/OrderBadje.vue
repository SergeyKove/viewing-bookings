<template>
  <div v-if="item.status !== 'Banquet'" class="badje neutral">
    {{ item.status }}
  </div>
</template>

<script setup>
const props = defineProps({
  item: Object,
})
</script>

<style scoped>
.badje {
  height: 12px;
  padding: 2px;
  border-radius: 4px;
  font-family: 'Inter';
  font-weight: 600;
  font-size: 8px;
  line-height: 8px;
  letter-spacing: 0px;
  display: flex;
  align-self: flex-start;
}
.neutral {
  color: rgba(255, 255, 255, 1);
  background-color: rgba(255, 255, 255, 0.12);
}

.success {
  color: rgba(255, 255, 255, 1);
  background-color: rgba(74, 201, 155, 0.32);
}

.primary {
  color: rgba(255, 255, 255, 1);
  background-color: rgba(0, 122, 255, 1);
}

.primary-reverse {
  color: rgba(0, 151, 253, 1);
  background-color: rgba(0, 151, 253, 0.1);
}
</style>
