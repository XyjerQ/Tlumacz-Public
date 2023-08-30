<script setup lang="ts">
import { reactive, ref } from 'vue'
import * as apikey from '@/Api/apikey'

const emit = defineEmits(['transFrom'])
const prop = defineProps(['transTo', 'detectedLang'])

const transFrom = ref('pl')
const displayDetected = ref('')
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
    console.log(response.languages)
    if (response.languages == undefined) {
      alert('API has reached a limit. Sorry.')
    } else {
      response.languages.forEach((element: object) => {
        languages.push(element)
      })
    }
  })

const trans = () => {
  console.log(transFrom.value)
  emit('transFrom', transFrom.value)
}

const switchlang = () => {
  console.log(prop.transTo)
  transFrom.value = prop.transTo
  emit('transFrom', prop.transTo)
}

const detect = () => {
  if (displayDetected.value != prop.detectedLang) {
    for (let i = 0; i < languages.length; i++) {
      if (languages[i].language == prop.detectedLang) {
        displayDetected.value = `${languages[i].name} - Detected`
        return
      } else {
        displayDetected.value = ''
      }
    }
  }
}

defineExpose({
  switchlang,
  detect
})
</script>

<template>
  <select name="lang" id="getlanguages" @change="trans()" v-model="transFrom">
    <option v-if="displayDetected == ''" value="detectLang">Detect language</option>
    <option v-else value="detectLang">{{ displayDetected }}</option>
    <option v-for="language in languages" :key="language.language" :value="language.language">
      {{ language.name }}
    </option>
  </select>
</template>

<style scoped></style>
