<template>
  <div
    class="badje"
    :class="{
      neutral: item.status === 'New' || 'Closed' || 'Живая очередь' || 'Отменен',
      success: item.status === 'Bill',
      primary: item.status === 'Новая',
      'primary-reverse': item.status === 'Заявка',
    }"
  >
    <span v-if="item.status === 'New'"> Новый </span>
    <span v-if="item.status === 'Bill'"> Пречек </span>
    <span v-if="item.status === 'Closed'"> Закрытый </span>
    <span v-if="item.status === 'Живая очередь'"> Живая очередь </span>
    <span v-if="item.status === 'Новая'"> Ожидает подтверждения </span>
    <span v-if="item.status === 'Заявка'"> Ожидаем </span>
    <span v-if="item.status === 'Открыт'"> В зале </span>
    <span v-if="item.status === 'Отменен'"> Отменен </span>
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
