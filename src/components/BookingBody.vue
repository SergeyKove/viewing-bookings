<template>
  <h2 class="booking">Бронирования</h2>
  <div class="btn-date-block flex-column">
    <span class="text-grey btn-date-block_title">Дата</span>
    <div>
      <button v-for="item in restaurantData.available_days" @click="() => onClick(item)" autofocus>
        {{ moment(item).calendar() }}
      </button>
    </div>
  </div>
  <div class="floor-selection flex-column">
    <span class="text-grey btn-date-block_title">Отображаемые зоны</span>
    <div class="flex-row" style="align-items: center">
      <button>1 этаж</button>
      <button>2 этаж</button>
      <button>Банкетный зал</button>
    </div>
  </div>
</template>

<script setup>
import moment from 'moment-timezone'

const props = defineProps(['restaurantData'])
const model = defineModel()

function onClick(item) {
  model.value = item
}
</script>
