<template>
  <div class="card" :style="getBackground">
    <span v-if="item.name_for_reservation" style="font-size: 8px">№{{ item.id }}</span>
    <span v-if="item.name_for_reservation"
      >{{ item.name_for_reservation }};{{ item.num_people }}чел</span
    >
    <span v-if="item.type === 'ORDER' && item.status !== 'Banquet'">Заказ</span>
    <span v-if="item.type === 'ORDER' && item.status === 'Banquet'">Банкет</span>
    <OrderBadje v-if="item.status !== 'Banquet'" :item="item" />
    <span v-if="item.phone_number" style="font-size: 8px"
      ><PhoneIcon />{{ item.phone_number }}</span
    >
    <span
      >{{ moment(item.start_time).format('HH:mm') }}-{{
        moment(item.end_time).format('HH:mm')
      }}</span
    >
  </div>
</template>

<script setup>
import OrderBadje from '@/ui/badje/OrderBadje.vue'
import PhoneIcon from '@/ui/icon/PhoneIcon.vue'
import { computed, ref } from 'vue'
import moment from 'moment-timezone'

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
    return `background: linear-gradient(to right, ${colors.main} 4px, ${colors.main} 4px, ${colors.sub} 4px, ${colors.sub} 100%);`

  return 'background-color: rgba(128, 128, 128, 0.9);'
})
</script>

<style scoped>
.card {
  border-radius: 4px;
  padding: 4px 10px;
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
</style>
