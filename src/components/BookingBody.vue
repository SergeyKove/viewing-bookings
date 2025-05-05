<template>
  <div>
    <h2 class="booking">Бронирования</h2>

    <div class="btn-date-block flex-column">
      <span class="text-grey btn-date-block_title">Дата</span>

      <div>
        <button
          v-for="item in restaurantData.available_days"
          @click="() => onClick(item)"
          autofocus
          class="btn-date-block__button"
          :class="{ 'btn-date-block__button_active': item === model }"
        >
          {{ moment(item).calendar() }}
        </button>
      </div>
    </div>

    <div class="floor-selection flex-column">
      <span class="text-grey btn-date-block_title">Отображаемые зоны</span>

      <div class="flex-row" style="align-items: center">
        <button
          class="btn-date-block__button"
          :class="{ 'btn-date-block__button_active': zoneModel === undefined }"
          @click="() => (zoneModel = undefined)"
        >
          Все
        </button>

        <button
          v-for="zone in tableZones"
          class="btn-date-block__button"
          :class="{ 'btn-date-block__button_active': zone === zoneModel }"
          @click="() => (zoneModel = zone)"
        >
          {{ zone }}
        </button>
      </div>
    </div>
  </div>
</template>

<script setup>
import moment from 'moment-timezone'

const props = defineProps(['restaurantData', 'tableZones'])

const model = defineModel()
const zoneModel = defineModel('zone')

function onClick(item) {
  model.value = item
}
</script>

<style lang="css" scoped>
.btn-date-block_title {
  font-weight: 400;
  font-size: 11px;
  line-height: 14px;
  letter-spacing: 0px;
  margin-bottom: 5px;
}

.btn-date-block__button {
  height: 36px;
  padding: 4px 8px;
  background-color: rgba(255, 255, 255, 0.04);
  border-radius: 8px;
  margin-right: 8px;
  font-weight: 600;
  font-size: 11px;
  line-height: 14px;
  letter-spacing: 0px;
  vertical-align: middle;
}

.btn-date-block__button:hover {
  background-color: rgba(0, 122, 255, 1);
}

.btn-date-block__button_active {
  background-color: rgba(0, 122, 255, 1);
}
</style>
