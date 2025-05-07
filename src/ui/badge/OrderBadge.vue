<template>
  <div class="badge" :class="badgeLabel.class">
    <span>{{ badgeLabel.label }}</span>
  </div>
</template>

<script setup>
import { computed } from 'vue'

const props = defineProps({
  item: Object,
})

const badgeLabel = computed(() => getStatus(props.item.status))

const getStatus = (status) => {
  switch (status) {
    case 'New':
      return { label: 'Новый', class: 'neutral' }

    case 'Bill':
      return { label: 'Пречек', class: 'success' }

    case 'Closed':
      return { label: 'Закрытый', class: 'neutral' }

    case 'Живая очередь':
      return { label: 'Живая очередь', class: 'neutral' }

    case 'Новая':
      return { label: 'Ожидает подтверждения', class: 'primary' }

    case 'Заявка':
      return { label: 'Ожидаем', class: 'primary-reverse' }

    case 'Открыт':
      return { label: 'В зале', class: 'success' }

    case 'Отменен':
      return { label: 'Отменен', class: 'neutral' }

    default:
      return { label: 'Без статуса', class: '' }
  }
}
</script>

<style scoped>
.badge {
  height: 12px;
  padding: 2px;
  border-radius: 4px;
  font-family: 'Inter';
  font-weight: 600;
  font-size: 8px;
  line-height: 8px;
  letter-spacing: 0px;
  align-self: flex-start;
  white-space: nowrap;
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
