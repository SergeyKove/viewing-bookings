<template>
  <button v-for="item in restaurantData.available_days" @click="correntDate = item" class="btn">
    {{ item }}
  </button>
  <div class="flex-row">
    <div v-for="item in filtredTables" class="flex-column position-relative">
      <div
        class="flex-column header-cell"
        :style="{ width: `${widthCell}px`, height: `${heightHeaderCell}px` }"
      >
        <span> #{{ item.number }} {{ item.capacity + ' чел' }}</span>

        <span>{{ item.zone }}</span>
      </div>
      <div
        v-for="item in workTime"
        class="border"
        :style="{ width: `${widthCell}px`, height: `${heightCell}px` }"
      ></div>
      <div
        v-for="itm in item.orders"
        :style="{ top: itm.pos.top, height: itm.pos.height }"
        class="position-absolute block-order"
      >
        {{ itm.status }}
      </div>
      <div
        v-for="itm in item.reservations"
        :style="{ top: itm.pos.top, height: itm.pos.height }"
        class="position-absolute block-reservation"
      >
        {{ itm.status }}
      </div>
    </div>
  </div>
  <div class="btn-size-table">
    Масштаб
    <button @click="increaseSize()">+</button>
    <button @click="reduceSize()">-</button>
  </div>
</template>

<script setup>
import { computed, ref } from 'vue'
import moment from 'moment-timezone'
import axios from 'axios'

moment.tz.setDefault('Asia/Vladivostok')

const restaurantData = ref([])
const correntDate = ref('')

async function getRestaurant() {
  const data = await axios.get('https://hh.frontend.ark.software/api/booking')
  restaurantData.value = data.data
  correntDate.value = data.data.current_day
}
getRestaurant()

const timeWorkRestaurant = function () {
  const time_object = []
  // const [hoursOpen, minutesOpen] = items.value.restaurant.opening_time.split(':')
  // const [hoursClose, minutesClose] = items.value.restaurant.closing_time.split(':')

  let hour = 11
  let minute = 0
  let end_hour = 23
  let end_minute = 40

  while (hour < end_hour || (hour == end_hour && minute <= end_minute)) {
    time_object.push({ hour, minute })
    minute += 30
    if (minute >= 60) {
      minute -= 60
      hour += 1
    }
  }

  return time_object
}
const workTime = timeWorkRestaurant()

const filtredTables = computed(() => {
  return restaurantData.value.tables?.map((table) => {
    return {
      ...table,
      orders: table.orders
        .filter((item) => moment(item.start_time).isSame(correntDate.value, 'day') === true)
        .map((order) => {
          return {
            ...order,
            pos: getPos(order.start_time, order.end_time),
          }
        }),
      reservations: table.reservations
        .filter((item) => moment(item.seating_time).isSame(correntDate.value, 'day') === true)
        .map((reservation) => {
          return {
            ...reservation,
            pos: getPos(reservation.seating_time, reservation.end_time),
          }
        }),
    }
  })
})

const widthCell = ref(80)
const heightCell = ref(40)
const heightHeaderCell = ref(48)

const pixelMinute = computed(() => {
  return heightCell.value / 30
})

function getPos(start_time, end_time) {
  const [hoursOpen, minutesOpen] = restaurantData.value.restaurant.opening_time.split(':')
  const startAddDateTime = moment(start_time).hour(hoursOpen).minute(minutesOpen)
  const top = moment(start_time).diff(startAddDateTime, 'minutes')
  const height = moment(end_time).diff(moment(start_time), 'minutes')
  return {
    top: `${(top * pixelMinute.value + heightHeaderCell.value).toFixed()}px`,
    height: `${(height * pixelMinute.value).toFixed()}px`,
  }
}

function increaseSize() {
  widthCell.value += 16
  heightCell.value += 4
  heightHeaderCell.value += 4
}

function reduceSize() {
  widthCell.value -= 16
  heightCell.value -= 4
  heightHeaderCell.value -= 4
}
</script>

<style scoped>
.flex-row {
  display: flex;
  flex-direction: row;
}

.flex-column {
  display: flex;
  flex-direction: column;
}

.header-cell {
  font-weight: 400;
  font-size: 11px;
  text-align: center;
}

.border {
  border-right: 1px solid black;
  border-top: 1px solid black;
}

.btn {
  width: 66px;
  height: 36px;
  min-height: 36px;
  border-radius: 8px;
  padding: 4px 8px;
  margin-right: 8px;
}

.position-relative {
  position: relative;
}

.position-absolute {
  position: absolute;
}

.block-order {
  width: 80px;
  background-color: gray;
  border: 1px solid red;
}

.block-reservation {
  width: 80px;
  background-color: rgb(179, 176, 176);
  border: 1px solid rgb(30, 255, 0);
}

.btn-size-table {
  position: fixed;
  right: 20px;
  bottom: 20px;
  background-color: yellow;
  padding: 5px;
  border-radius: 8px;
}
</style>
