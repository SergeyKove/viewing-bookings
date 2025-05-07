<template>
  <BookingHeader :restaurant="restaurantData.restaurant" />

  <BookingBody
    :restaurantData="restaurantData"
    v-model="currentDate"
    v-model:zone="tableZoneModel"
    :tableZones="tableZones"
  />

  <div class="flex-row table">
    <TableCurrentTime
      v-if="openingTime"
      :cellParams="cellParams"
      :openingTime="openingTime"
      :columnCount="filtredTables.length"
    />
    <TableTime :cellParams="cellParams" :workTime="workTime" />
    <template v-for="item in filtredTables">
      <TableColumn
        :item="item"
        :cellParams="cellParams"
        :workTime="workTime"
        :openingTime="openingTime"
      />
    </template>
  </div>

  <TableSizeButtons
    v-model:width="widthCell"
    v-model:height="heightCell"
    v-model:heightHeader="heightHeaderCell"
  />
</template>

<script setup>
import BookingHeader from './BookingHeader.vue'
import BookingBody from './BookingBody.vue'
import TableColumn from '@/components/bookingTable/TableColumn.vue'
import TableSizeButtons from '@/components/bookingTable/TableSizeButtons.vue'
import TableTime from './bookingTable/TableTime.vue'
import TableCurrentTime from './bookingTable/TableCurrentTime.vue'

import { computed, ref } from 'vue'
import moment from 'moment-timezone'
import axios from 'axios'

import { CELL_WIDTH, CELL_HEIGHT, HEADER_CELL_HEIGHT, RESERVATION_TYPE } from '@/app-variables.js'

moment.tz.setDefault('Asia/Vladivostok')

const widthCell = ref(CELL_WIDTH)
const heightCell = ref(CELL_HEIGHT)
const heightHeaderCell = ref(HEADER_CELL_HEIGHT)

const restaurantData = ref([])
const currentDate = ref('')
const openingTime = computed(() => restaurantData.value.restaurant?.opening_time)
const workTime = ref([])

async function getRestaurant() {
  const data = await axios.get('https://hh.frontend.ark.software/api/booking')

  restaurantData.value = data.data
  currentDate.value = data.data.current_day

  workTime.value = timeWorkRestaurant(
    data.data.restaurant.opening_time,
    data.data.restaurant.closing_time,
  )
}
getRestaurant()

const timeWorkRestaurant = (opening_time, closing_time) => {
  const time_object = []

  const start = opening_time.split(':')
  const end = closing_time.split(':')

  let hour = Number(start[0])
  let minute = Number(start[1])
  let end_hour = Number(end[0])
  let end_minute = Number(end[1])

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

const cellParams = computed(() => ({
  widthCell: widthCell.value,
  heightCell: heightCell.value,
  heightHeaderCell: heightHeaderCell.value,
}))

const normalizeStartTimeTables = computed(() => {
  if (restaurantData.value && restaurantData.value.tables) {
    return {
      ...restaurantData.value,
      tables: restaurantData.value.tables.map((item) => {
        const orders = item.orders.map((order) => ({
          ...order,
          type: RESERVATION_TYPE.ORDER,
        }))

        const reservations = item.reservations.map((reservation) => ({
          ...reservation,
          type: RESERVATION_TYPE.RESERVATION,
          start_time: reservation.seating_time,
        }))

        return {
          ...item,
          normalized: [...orders, ...reservations],
        }
      }),
    }
  }

  return []
})

const filtredTables = computed(() => {
  const tables = normalizeStartTimeTables.value.tables
    ? tableZoneModel.value
      ? normalizeStartTimeTables.value.tables.filter((item) => item.zone === tableZoneModel.value)
      : normalizeStartTimeTables.value.tables
    : []

  return tables.map((table) => {
    return {
      ...table,
      normalized: table.normalized.filter((item) => {
        return moment(item.start_time).isSame(currentDate.value, 'day') === true
      }),
    }
  })
})

const tableZoneModel = ref(undefined)
const tableZones = computed(() => {
  if (normalizeStartTimeTables.value && normalizeStartTimeTables.value.tables) {
    let items = {}
    normalizeStartTimeTables.value.tables.forEach((table) => {
      if (table.zone && !(table.zone in items)) items[table.zone] = table.zone
    })
    return Object.keys(items)
  }

  return []
})
</script>

<style scoped></style>
