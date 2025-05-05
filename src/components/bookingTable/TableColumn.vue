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
      :style="getCardStyle(columnItem.pos)"
      class="position-absolute block-reservation"
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

const columnItems = computed(() => {
  if (props.item && props.item.normalized) {
    const sorted = props.item.normalized.sort((a, b) => moment(a.start_time).diff(b.start_time))

    let result = []

    let countSpaces = 0
    let endDate = undefined

    sorted.forEach((itm) => {
      let pos = getPos(itm.start_time, itm.end_time)

      if (endDate && moment(itm.start_time).isBefore(endDate)) {
        pos['marginLeft'] = `${MARGIN_SLICE + countSpaces * MARGIN_SLICE}`
        countSpaces++
        if (moment(itm.end_time).isAfter(endDate)) endDate = itm.end_time
      } else {
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

const getCardStyle = (pos) => {
  return {
    top: `${pos.top}px`,
    height: `${pos.height}px`,
    marginLeft: `${pos.marginLeft}px`,
    width: pos.width ? pos.width : pos.marginLeft ? `calc(100% - ${pos.marginLeft}px)` : '100%',
  }
}

const pixelMinute = computed(() => {
  return props.cellParams.heightCell / 30
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
</script>

<style scoped>
.block-reservation:hover {
  z-index: 1000;
  background-color: rgba(0, 0, 0, 0.705) !important;
  overflow: visible;
}
</style>
