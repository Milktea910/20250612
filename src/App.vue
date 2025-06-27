<template>
  <v-app>
    <v-app-bar class="bg-blue">
      <v-container class="d-flex align-center">
        <v-app-bar-title>番茄鐘</v-app-bar-title>
        <v-btn prepend-icon="mdi-home" to="/">首頁</v-btn>
        <v-btn prepend-icon="mdi-format-list-bulleted" to="/list">事項</v-btn>
        <v-btn prepend-icon="mdi-cog" to="/settings">設定</v-btn>
        <v-btn
          v-if="theme.global.name.value === 'dark'"
          prepend-icon="mdi-white-balance-sunny"
          @click="toggleTheme()"
          >淺色主題</v-btn
        >
        <v-btn
          v-if="theme.global.name.value === 'light'"
          prepend-icon="mdi-moon-waning-crescent"
          @click="toggleTheme()"
          >深色主題</v-btn
        >
      </v-container>
    </v-app-bar>
    <v-main>
      <router-view v-slot="{ Component }">
        <keep-alive include="HomeView">
          <component :is="Component" />
        </keep-alive>
      </router-view>
    </v-main>
  </v-app>
</template>

<script setup>
import { useTheme } from 'vuetify'
import { useSettingsStore } from '@/stores/setting'

const theme = useTheme()
const setting = useSettingsStore()

function toggleTheme() {
  theme.global.name.value = theme.global.current.value.dark ? 'light' : 'dark'
  console.log(setting.theme)
  setting.theme = theme.global.current.value.dark ? 'white' : 'black'
}
</script>
<style scoped>
.bg-blue {
  background: rgb(0, 110, 255);
}
</style>
