<script setup lang="ts">
import { reactive, ref } from 'vue'
import * as apikey from '@/Api/apikey'

const emit = defineEmits(['transTo'])
const prop = defineProps(['transFrom'])

const transTo = ref('en')
const languages: Array<langObject> = reactive([])
type langObject = {
  language?: string
  name?: string
}

const url = 'https://deep-translate1.p.rapidapi.com/language/translate/v2/languages'
const options = {
  method: 'GET',
  headers: apikey.headers
}

fetch(url, options)
  .then((response) => response.json())
  .then((response) => {
    // console.log(response.languages)
    if (response.languages != undefined) {
      response.languages.forEach((element: object) => {
        languages.push(element)
      })
    }
  })

const trans = () => {
  // console.log(transTo.value)
  emit('transTo', transTo.value)
}

const switchlang = () => {
  // console.log(prop.transFrom)
  transTo.value = prop.transFrom
  emit('transTo', prop.transFrom)
}

defineExpose({
  switchlang
})
</script>

<template>
  <select name="lang" id="getlanguages" @change="trans()" v-model="transTo">
    <option v-for="language in languages" :key="language.language" :value="language.language">
      {{ language.name }}
    </option>
  </select>
</template>

<style scoped></style>
