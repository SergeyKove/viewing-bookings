<template>
  <div class="card" :style="getCardStyle">
    <span v-if="item.name_for_reservation" class="small-text">№{{ item.id }}</span>

    <span v-if="item.name_for_reservation"
      >{{ item.name_for_reservation }};{{ item.num_people }}чел</span
    >

    <span v-if="item.type === RESERVATION_TYPE.ORDER">{{
      item.status === 'Banquet' ? 'Банкет' : 'Заказ'
    }}</span>

    <OrderBadge v-if="item.status !== 'Banquet'" :item="item" />

    <span v-if="item.phone_number" class="phone-icon"><PhoneIcon />{{ item.phone_number }}</span>

    <span
      >{{ moment(item.start_time).format('HH:mm') }} -
      {{ moment(item.end_time).format('HH:mm') }}</span
    >
  </div>
</template>

<script setup>
import OrderBadge from '@/ui/badge/OrderBadge.vue'
import PhoneIcon from '@/ui/icon/PhoneIcon.vue'

import { computed } from 'vue'
import moment from 'moment-timezone'
import { RESERVATION_TYPE } from '@/app-variables.js'

const props = defineProps({
  item: Object,
})

const statusColors = {
  Banquet: { main: '#7B439E', sub: '#B348F729' },
  Closed: { main: '#7FD7CC', sub: '#7FD7CC29' },
  Bill: { main: '#7FD7CC', sub: '#7FD7CC29' },
  New: { main: '#7FD7CC', sub: '#7FD7CC29' },
  'Живая очередь': { main: '#007AFF', sub: '#0097FD29' },
  Новая: { main: '#FF7043', sub: '#FF704329' },
  Заявка: { main: '#FF7043', sub: '#FF704329' },
  Отменен: { main: '#FF7043', sub: '#FF704329' },
  Открыт: { main: '#FF7043', sub: '#FF704329' },
  Закрыт: { main: '#FF7043', sub: '#FF704329' },
}

const getBackground = computed(() => {
  if (!props.item || !props.item.status) return ''

  const colors =
    props.item.status && props.item.status in statusColors
      ? statusColors[props.item.status]
      : undefined

  if (colors)
    return `linear-gradient(to right, ${colors.main} 4px, ${colors.main} 4px, ${colors.sub} 4px, ${colors.sub} 100%)`

  return 'rgba(128, 128, 128, 0.9)'
})

const getCardStyle = computed(() => {
  if (!props.item || !props.item.status) return ''

  const pos = props.item.pos
  return {
    background: getBackground.value,
    top: `${pos.top}px`,
    height: `${pos.height}px`,
    marginLeft: typeof pos.marginLeft === 'number' ? `${pos.marginLeft}px` : pos.marginLeft,
    width: pos.width
      ? `${pos.width}%`
      : pos.marginLeft
        ? `calc(100% - ${pos.marginLeft}px)`
        : '100%',
  }
})
</script>

<style scoped>
.card {
  border-radius: 4px;
  padding: 4px 8px;
  display: flex;
  flex-direction: column;
  flex-wrap: nowrap;
  font-family: 'Inter';
  font-weight: 400;
  font-size: 10px;
  line-height: 14px;
  letter-spacing: 0px;
  overflow: hidden;
}

.phone-icon {
  font-size: 8px;
  white-space: nowrap;
}
</style>
