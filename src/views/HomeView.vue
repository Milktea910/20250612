<template>
  <v-container>
    <v-row>
      <v-col cols="12">
        <h1>目前事項</h1>
        <h2>{{ lists.currentItem }}</h2>
        <DigitNumber
          v-for="(data, i) in timeLeftText"
          :key="i"
          :color="setting.theme"
          :data="data"
        />
      </v-col>
      <v-col cols="12">
        <v-btn
          :disabled="
            status === STATUS.COUNTING ||
            (lists.currentItem.length === 0 && lists.items.length === 0)
          "
          icon="mdi-play"
          @click="startTimer()"
        />
        <v-btn :disabled="status !== STATUS.COUNTING" icon="mdi-pause" @click="pause" />
        <v-btn :disabled="lists.currentItem.length === 0" icon="mdi-skip-next" @click="finish" />
      </v-col>
    </v-row>
  </v-container>
</template>

<script setup>
import { useWebNotification } from '@vueuse/core'
import { computed, ref } from 'vue'
import DigitNumber from '@/components/DigitNumber.vue'
import { useListStore } from '@/stores/list'
import { useSettingsStore } from '@/stores/setting'

const lists = useListStore()
const setting = useSettingsStore()

const STATUS = {
  STOP: 0,
  COUNTING: 1,
  PAUSE: 2,
}
const status = ref(STATUS.STOP)

let timer = 0

const startTimer = () => {
  if (status.value === STATUS.STOP && lists.items.length > 0) {
    lists.setCurrentItem()
  }

  status.value = STATUS.COUNTING

  timer = setInterval(() => {
    lists.countdown()

    if (lists.timeleft <= 0) {
      finish(timer)
    }
  }, 1000)
}

const pause = () => {
  clearInterval(timer)
  status.value = STATUS.PAUSE
}

const finish = () => {
  clearInterval(timer)

  status.value = STATUS.STOP

  const audio = new Audio()
  audio.src = setting.selectedAlarm.file
  audio.play()

  const { show, isSupported } = useWebNotification({
    title: '事項完成',
    body: lists.currentItem,
    icon: new URL('@/assets/tomato.png', import.meta.url).href,
  })
  if (isSupported.value) {
    show()
  }

  lists.setFinishItem()

  if (lists.items.length > 0) {
    startTimer()
  }
}

const timeLeftText = computed(() => {
  const m = Math.floor(lists.timeleft / 60)
    .toString()
    .padStart(2, '0')
  const s = (lists.timeleft % 60).toString().padStart(2, '0')
  return m + ':' + s
})
</script>

<style></style>
