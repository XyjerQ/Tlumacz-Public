<script setup lang="ts">
import { onMounted } from 'vue'
import { Icon } from '@iconify/vue'

onMounted(() => {
  let toggle: any = document.getElementById('btnSwitch')
  toggle.addEventListener('click', () => {
    if (document.documentElement.getAttribute('data-bs-theme') == 'dark') {
      document.documentElement.setAttribute('data-bs-theme', 'light')
    } else {
      document.documentElement.setAttribute('data-bs-theme', 'dark')
    }
  })
})
</script>

<template>
  <span id="btnSwitch"><Icon class="tglMoon" icon="bi:moon-fill" :inline="true" /></span>
</template>

<style lang="scss" scoped>
#btnSwitch {
  cursor: pointer;
  padding-top: 5px;
  .tglMoon {
    font-size: 25px;
  }
}
</style>
