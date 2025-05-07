<template>
  <div class="flex-column position-relative">
    <TableColumnHeader
      :item="item"
      :width="cellParams.widthCell"
      :height="cellParams.heightHeaderCell"
    />

    <div
      v-for="item in workTime"
      class="border"
      :style="{ width: `${cellParams.widthCell}px`, height: `${cellParams.heightCell}px` }"
    ></div>

    <TableCard
      v-for="columnItem in columnItems"
      :item="columnItem"
      class="position-absolute block-reservation cursor-pointer"
    >
    </TableCard>
  </div>
</template>

<script setup>
import TableCard from './TableCard.vue'
import TableColumnHeader from './TableColumnHeader.vue'

import moment from 'moment-timezone'
import { computed, ref } from 'vue'

const props = defineProps({
  item: Object,
  workTime: Array,
  cellParams: Object,
  openingTime: String,
})

const MARGIN_SLICE = 4

const getRowWidth = (arr, idx, counter = []) => {
  const current = arr[idx]
  const next = arr[idx + 1]

  counter.push(current.id)

  if (next && moment(next.start_time).diff(moment(current.start_time), 'minutes') <= 30) {
    return getRowWidth(arr, idx + 1, counter)
  }

  return counter
}

const columnItems = computed(() => {
  if (props.item && props.item.normalized) {
    const sorted = props.item.normalized.sort((a, b) => moment(a.start_time).diff(b.start_time))

    let result = []

    let countSpaces = 0
    let endDate = undefined

    const widthCounter = {}

    sorted.forEach((itm, idx) => {
      if (!(itm.id in widthCounter)) {
        // получаем последовательность элементов
        const itemsPerRow = getRowWidth(sorted, idx)

        if (itemsPerRow.length > 1)
          // сохраняем, если две и более колонки
          itemsPerRow.forEach((id, idx) => {
            widthCounter[id] = {
              order: idx,
              itemsCount: itemsPerRow.length,
            }
          })
      }

      let pos = getPos(itm.start_time, itm.end_time)

      // сохраняем ширину, если колонки
      if (itm.id in widthCounter) pos['width'] = 100 / widthCounter[itm.id].itemsCount

      // задаем отступ слева
      if (endDate && moment(itm.start_time).isBefore(endDate)) {
        if (itm.id in widthCounter && widthCounter[itm.id].order !== 0) {
          // если колонки, и не первый элемент
          const order = widthCounter[itm.id].order
          const itemsCount = widthCounter[itm.id].itemsCount

          pos['marginLeft'] = `${(100 / itemsCount) * order}%`
        } else {
          pos['marginLeft'] = MARGIN_SLICE + countSpaces * MARGIN_SLICE
          countSpaces++
        }

        if (moment(itm.end_time).isAfter(endDate)) endDate = itm.end_time
      } else {
        // обнуляем отступы
        countSpaces = 0
        endDate = itm.end_time
      }

      result.push({
        ...itm,
        pos,
      })
    })

    return result
  }

  return []
})

function getPos(start_time, end_time) {
  const [hoursOpen, minutesOpen] = props.openingTime.split(':')

  const startAddDateTime = moment(start_time).hour(hoursOpen).minute(minutesOpen)
  const top = moment(start_time).diff(startAddDateTime, 'minutes')
  const height = moment(end_time).diff(moment(start_time), 'minutes')

  return {
    top: (top * pixelMinute.value + props.cellParams.heightHeaderCell).toFixed(),
    height: (height * pixelMinute.value).toFixed(),
  }
}

const pixelMinute = computed(() => {
  return props.cellParams.heightCell / 30
})
</script>

<style scoped>
.block-reservation:hover {
  z-index: 1000;
  background-color: rgba(0, 0, 0, 0.5) !important;
  backdrop-filter: blur(2px);
  overflow: visible;
  width: 100% !important;
}
</style>
