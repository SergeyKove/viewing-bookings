<template>
  <BookingHeader :restaurant="restaurantData.restaurant" />
  <BookingBody :restaurantData="restaurantData" v-model="correntDate" />
  <div class="flex-row table">
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
        :style="{
          top: `${itm.pos.top}px`,
          height: `${itm.pos.height}px`,
          marginLeft: `${itm.pos.marginLeft}px`,
        }"
        class="position-absolute block-order"
      >
        {{ itm.status }}
      </div>
      <div
        v-for="itm in item.reservations"
        :style="{
          top: `${itm.pos.top}px`,
          height: `${itm.pos.height}px`,
          marginLeft: `${itm.pos.marginLeft}px`,
        }"
        class="position-absolute block-reservation"
      >
        {{ itm.status }}
      </div>
    </div>
  </div>
  <div class="btn-size-table">
    <span>Масштаб</span>
    <div>
      <button @click="increaseSize()">+</button>
      <button @click="reduceSize()">-</button>
    </div>
  </div>
</template>

<script setup>
import BookingHeader from './BookingHeader.vue'
import BookingBody from './BookingBody.vue'
import { computed, ref } from 'vue'
import moment from 'moment-timezone'
import axios from 'axios'

moment.locale('ru')
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
    top: (top * pixelMinute.value + heightHeaderCell.value).toFixed(),
    height: (height * pixelMinute.value).toFixed(),
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

// function adjustMargins(elements) {
//   for (let i = 0; i < elements.length; i++) {
//     const current = elements[i].pos
//     for (let j = 1; j < elements.length; j++) {
//       const next = elements[j].pos

//       if (current.top < next.top + next.height && current.top + current.height > next.top) {
//         next.marginLeft = parseInt(next.marginLeft || 0) + 4
//       }
//     }
//   }
//   return elements
// }

// const addingMargin = computed(() => {
//   return filtredTables.value?.map((table) => {
//     return {
//       ...table,
//       orders: adjustMargins(table.orders),
//       reservations: adjustMargins(table.reservations),
//     }
//   })
// })
</script>

<style></style>
