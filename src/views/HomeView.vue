<script setup lang="ts">
import { onMounted, ref } from 'vue'
import TransFrom from '@/components/TransFrom.vue'
import TransTo from '@/components/TransTo.vue'
import * as apikey from '@/Api/apikey'
import { Icon } from '@iconify/vue'
import axios from 'axios'

const langSwitchRef = ref<any>(null)
const langSwitchRef2 = ref<any>(null)
const textarea = ref<any>(null)
const textarea2 = ref<any>(null)

const TextInput = ref('')
const TextOutput = ref('')
const TextFromLang = ref('pl')
const TextFromLangTranslate = ref('pl')
const TextToLang = ref('en')
const detectedLang = ref('')
const numberOfSymbols = ref(0)

onMounted(() => {
  CheckIfDetect()
})

const CheckIfDetect = async () => {
  numberOfSymbols.value = TextInput.value.length
  if (TextFromLang.value == 'detectLang') {
    const options = {
      method: 'POST',
      url: 'https://deep-translate1.p.rapidapi.com/language/translate/v2/detect',
      headers: apikey.headers,
      data: {
        q: TextInput.value
      }
    }
    try {
      const response = await axios.request(options)
      console.log(response.data)
      TextFromLangTranslate.value = response.data.data.detections[0].language
      detectedLang.value = response.data.data.detections[0].language
    } catch (error) {
      console.error(error)
    }
    GetTranslate()
  } else {
    TextFromLangTranslate.value = TextFromLang.value
    detectedLang.value = ''
    GetTranslate()
  }
}

const GetTranslate = async () => {
  const options = {
    method: 'POST',
    url: 'https://deep-translate1.p.rapidapi.com/language/translate/v2',
    headers: apikey.headers,
    data: {
      q: TextInput.value,
      source: TextFromLangTranslate.value,
      target: TextToLang.value
    }
  }
  if (TextFromLang.value == TextToLang.value) {
    langSwitchRef.value.switchlang()
    langSwitchRef2.value.switchlang()
  } else {
    if (TextInput.value != '') {
      try {
        const response = await axios.request(options)
        console.log(response.data.data.translations.translatedText)
        TextOutput.value = response.data.data.translations.translatedText
      } catch (error) {
        console.error(error)
        alert('API has reached a limit. Sorry.')
      }
    }
  }
  langSwitchRef.value.detect()
}
const transFrom = (langFrom: string) => {
  TextFromLang.value = langFrom
  CheckIfDetect()
}
const transTo = (langTo: string) => {
  TextToLang.value = langTo
  CheckIfDetect()
}
const switchlang = () => {
  TextInput.value = TextOutput.value
  langSwitchRef.value.switchlang()
  langSwitchRef2.value.switchlang()
}
const resize = () => {
  textarea.value.style.height = 'auto'
  textarea.value.style.height = textarea.value.scrollHeight + 'px'
  textarea2.value.style.height = 'auto'
  textarea2.value.style.height = textarea2.value.scrollHeight + 'px'
}
</script>

<template>
  <main>
    <div class="backPg">
      <div class="content">
        <div class="langSelect">
          <div class="transFrom">
            <TransFrom
              @transFrom="transFrom"
              :transTo="TextToLang"
              :detectedLang="detectedLang"
              ref="langSwitchRef"
            />
          </div>
          <div class="switchLang">
            <button @click="switchlang()">
              <Icon icon="octicon:arrow-switch-16" :inline="true" />
            </button>
          </div>
          <div class="transTo">
            <TransTo @transTo="transTo" :transFrom="TextFromLangTranslate" ref="langSwitchRef2" />
          </div>
        </div>
        <div class="textInOut">
          <div class="textIn">
            <textarea
              type="text"
              v-model="TextInput"
              @keyup="CheckIfDetect()"
              ref="textarea"
              rows="1"
              @focus="resize()"
              @keydown="resize()"
              @change="resize()"
              maxlength="2000"
            />
            <span>{{ numberOfSymbols }} / 2000</span>
          </div>
          <div class="textOut">
            <textarea ref="textarea2" v-model="TextOutput" placeholder="Translation" />
          </div>
        </div>
      </div>
    </div>
  </main>
</template>

<style lang="scss" scoped>
.backPg {
  height: 60vh;
  justify-content: center;
  display: flex;
  .content {
    text-align: center;
    max-width: 90vw;
    padding-top: 30px;
  }
}
.langSelect {
  text-align: left;
  padding: 10px;
  font-size: 1.3rem;

  select {
    border: 0px;
    background: none;
    border-bottom: 1px solid;
    border-radius: 3px;
    padding-bottom: 4px;
  }
  .transFrom {
    float: left;
    width: calc(50% - 20px);
  }
  .switchLang {
    float: left;
    width: 40px;
    text-align: center;
    button {
      border: none;
      background: none;
      font-size: 26px;
    }
  }
  .transTo {
    float: left;
    width: calc(50% - 20px);
  }
}
.textInOut {
  min-width: 90vw;
  padding-top: 0px;
  display: flex;
  font-size: 1.5rem;
  min-height: 165px;

  .textIn {
    width: calc(50% - 10px);
    float: left;
    margin-right: 10px;
    border: 1px solid;
    border-radius: 8px;
    padding-top: 15px;
    padding-inline: 15px;
    text-align: right;
    textarea {
      min-height: 100px;
      border: 0px;
      background: none;
      width: 100%;
      resize: vertical;
      resize: none;
      outline: none;
      box-shadow: none;
      overflow: hidden;
    }
    span {
      font-size: 1rem;
    }
  }
  .textOut {
    width: calc(50% - 10px);
    float: left;
    margin-left: 10px;
    border: 1px solid;
    border-radius: 8px;
    padding: 15px;
    text-align: left;
    textarea {
      width: 100%;
      border: 0px;
      background: none;
      pointer-events: none;
      resize: none;
      outline: none;
      box-shadow: none;
      overflow: hidden;
    }
  }
}

@media only screen and (max-width: 1100px) {
  .langSelect {
    font-size: 0.9rem;
    .switchLang {
      button {
        font-size: 20px;
      }
    }
  }
  .textInOut {
    display: grid;
    font-size: 1.25rem;
    .textIn {
      width: 100%;
      margin-right: 0px;
      margin-bottom: 7px;
      span {
        font-size: 0.9rem;
      }
    }
    .textOut {
      width: 100%;
      margin-left: 0px;
      margin-top: 7px;
      min-height: 127px;
    }
  }
}
@media only screen and (max-width: 400px) {
  .langSelect {
    font-size: 0.75rem;
    .switchLang {
      button {
        font-size: 17px;
      }
    }
  }
  .textInOut {
    font-size: 1.1rem;
  }
}
</style>
