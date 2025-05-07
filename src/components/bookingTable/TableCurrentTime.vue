<template>
  <div
    class="current-time"
    :style="{ top: `${currentTime.top}px`, width: `${widthCurrentTime}px` }"
  ></div>
</template>

<script setup>
import moment from 'moment'
import { computed } from 'vue'
const props = defineProps({
  cellParams: Object,
  openingTime: String,
  columnCount: Number,
})

const widthCurrentTime = computed(() => {
  return props.columnCount * props.cellParams.widthCell
})

const currentTime = computed(() => {
  return getPos(moment().format())
})

function getPos(start_time) {
  const [hoursOpen, minutesOpen] = props.openingTime.split(':')

  const startAddDateTime = moment(start_time).hour(hoursOpen).minute(minutesOpen)
  const top = moment(start_time).diff(startAddDateTime, 'minutes')

  return {
    top: (top * pixelMinute.value + props.cellParams.heightHeaderCell).toFixed(),
  }
}

const pixelMinute = computed(() => {
  return props.cellParams.heightCell / 30
})
</script>

<style scoped>
.current-time {
  position: absolute;
  height: 1px;
  background: rgba(255, 112, 67, 1);
  z-index: 1002;
  margin-left: 30px;
}
</style>
