<template>
  <v-container style="width: 65%">
    <v-row>
      <v-col cols="12">
        <h1 class="text-center">鈴聲設定</h1>
      </v-col>
      <v-col cols="12">
        <v-table>
          <thead>
            <tr>
              <th>名稱</th>
              <th>試聽</th>
              <th>選擇</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="alarm in setting.alarms" :key="alarm.id">
              <td>
                {{ alarm.name }}
              </td>
              <td>
                <audio controls :src="alarm.file" />
              </td>
              <td>
                <v-radio-group v-model="setting.selected" hide-details>
                  <v-radio :value="alarm.id" />
                </v-radio-group>
              </td>
            </tr>
          </tbody>
        </v-table>
      </v-col>
    </v-row>
  </v-container>
</template>

<script setup>
import { useSettingsStore } from '@/stores/setting'

const setting = useSettingsStore()
</script>
