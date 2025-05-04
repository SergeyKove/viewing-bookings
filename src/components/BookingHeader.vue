<template>
  <header class="header flex-row">
    <h1 class="header-title">AIRESTO | {{ restaurant?.restaurant_name }}</h1>
    <div class="flex-row" style="align-items: center; gap: 8px">
      <div class="search">
        <SearchIcon class="search-icon" />
        <input type="text" placeholder="⌘+Л поиск по имени" class="search-field" />
      </div>
      <button class="header-btn-switchBg">
        <SunIcon />
      </button>
      <button class="header-btn-exit flex-row">
        <ExitIcon />
        <span class="header-btn-exit_title">Выйти</span>
      </button>
    </div>
  </header>
</template>

<script setup>
import SunIcon from '@/ui/icon/SunIcon.vue'
import ExitIcon from '@/ui/icon/ExitIcon.vue'
import SearchIcon from '@/ui/icon/SearchIcon.vue'
const props = defineProps(['restaurant'])
</script>

<style></style>
