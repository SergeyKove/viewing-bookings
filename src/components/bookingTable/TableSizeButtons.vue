<template>
  <div class="banner">
    <span class="banner-label">Масштаб</span>

    <div class="banner-box">
      <button @click="increaseSize()" class="banner-actions">+</button>
      <button @click="reduceSize()" :disabled="MIN_CELL_WIDTH >= widthCell" class="banner-actions">
        -
      </button>
    </div>
  </div>
</template>

<script setup>
import {
  CELL_WIDTH_SCALE_STEP,
  CELL_HEIGHT_SCALE_STEP,
  HEADER_CELL_HEIGHT_SCALE_STEP,
  MIN_CELL_WIDTH,
} from '@/app-variables.js'

const widthCell = defineModel('width')
const heightCell = defineModel('height')
const heightHeaderCell = defineModel('heightHeader')

function increaseSize() {
  widthCell.value += CELL_WIDTH_SCALE_STEP
  heightCell.value += CELL_HEIGHT_SCALE_STEP
  heightHeaderCell.value += HEADER_CELL_HEIGHT_SCALE_STEP
}

function reduceSize() {
  widthCell.value -= CELL_WIDTH_SCALE_STEP
  heightCell.value -= CELL_HEIGHT_SCALE_STEP
  heightHeaderCell.value -= HEADER_CELL_HEIGHT_SCALE_STEP
}
</script>

<style scoped>
.banner {
  position: fixed;
  right: 200px;
  bottom: 20px;
  background-color: rgba(34, 34, 34, 1);
  padding: 8px;
  border: 1px solid rgba(255, 255, 255, 0.16);
  border-radius: 8px;
  z-index: 1001;
}

.banner-box {
  display: flex;
  justify-content: space-between;
}

.banner-label {
  font-weight: 600;
  font-size: 13px;
  line-height: 16px;
  letter-spacing: 0px;
}

.banner-actions {
  width: 24px;
  height: 24px;
  display: flex;
  align-items: center;
  justify-content: center;
  background-color: rgba(255, 255, 255, 0.08);
  border-radius: 4px;
}

.banner-actions:hover {
  background-color: blue;
  cursor: pointer;
}

.banner-actions:disabled {
  cursor: not-allowed;
}
</style>
