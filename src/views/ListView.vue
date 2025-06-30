<template>
  <v-container style="width: 65%">
    <v-row>
      <v-col cols="12">
        <h1 class="text-center">未完成</h1>
      </v-col>
      <v-col cols="12">
        <!-- <v-form @submit.prevent="onAddFormSubmit"> -->
        <v-text-field
          ref="inputTextField"
          v-model="input"
          append-icon="mdi-plus"
          clearable
          label="新增事項"
          :rules="[rules.length, rules.required]"
          @click:append="onAddFormSubmit"
          @keydown.enter="onAddFormSubmit"
        />
        <!-- </v-form> -->
        <v-table>
          <thead>
            <tr>
              <th>事項</th>
              <th>操作</th>
            </tr>
          </thead>
          <tbody>
            <tr v-if="lists.items.length === 0">
              <td colspan="2">沒有項目</td>
            </tr>
            <tr v-for="(list, i) in lists.items" :key="list.id">
              <td>
                <v-text-field
                  v-show="list.edit"
                  ref="editTextField"
                  v-model="list.model"
                  autofocus
                  :rules="[rules.required, rules.length]"
                />
                <span v-show="!list.edit">{{ list.text }}</span>
              </td>
              <td>
                <template v-if="list.edit">
                  <v-btn icon="mdi-check" @click="onEditSubmit(list.id, i)" />
                  <v-btn icon="mdi-undo" @click="lists.cancelEdit(list.id)" />
                </template>
                <template v-else>
                  <v-btn icon="mdi-pencil" @click="lists.editItem(list.id)" />
                  <v-btn icon="mdi-delete" @click="lists.delItem(list.id)" />
                </template>
              </td>
            </tr>
          </tbody>
        </v-table>
      </v-col>
      <v-col cols="12">
        <h1 class="text-center">已完成</h1>
        <v-table>
          <thead>
            <tr>
              <th>事項</th>
              <th>操作</th>
            </tr>
          </thead>
          <tbody>
            <tr v-if="lists.finishedItems.length === 0">
              <td colspan="2">沒有項目</td>
            </tr>
            <tr v-for="list in lists.finishedItems" :key="list.id">
              <td>{{ list.text }}</td>
              <td>
                <v-btn icon="mdi-delete" @click="lists.delFinishedItem(list.id)" />
              </td>
            </tr>
          </tbody>
        </v-table>
      </v-col>
    </v-row>
  </v-container>
</template>

<script setup>
import { ref, useTemplateRef } from 'vue'
import { useListStore } from '@/stores/list'

const lists = useListStore()
const input = ref('')
// const inputTextField = ref(null)
const inputTextField = useTemplateRef('inputTextField')
const editTextField = useTemplateRef('editTextField')

// const onAddFormSubmit = async(event) => {
//   const result = await event
//   if(!result.valid) return
//   lists.addItem(input.value)
//   input.value = ''
// }

const onAddFormSubmit = () => {
  if (!inputTextField.value.isValid) return
  lists.addItem(input.value)
  inputTextField.value.reset()
}
const onEditSubmit = (id, i) => {
  if (!editTextField.value[i].isValid) return
  lists.submitEdit(id)
}

const rules = {
  required: value => {
    // return Boolean(value)||'必填'
    return !!value || '必填'
  },
  length: value => {
    return value.length >= 3 || '必須3個字以上'
  },
}
</script>
