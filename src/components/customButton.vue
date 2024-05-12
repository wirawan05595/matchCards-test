<template>
  <div class="bg-btn-outside">
    <div class="flex items-center justify-center"
      :style="{ width: width, height: height }"
      ref="btnRef"
      :class="isClick ? 'bg-btn-onClick' : 'bg-btn'"
      @mousedown="onMouseDown"
      @mouseup="onMouseUp"
      @mouseleave="onMouseLeave"
      @click="onClick"
    >
      <q-icon :name="icon" v-if="icon !== ''" :size="iconSize"
      ></q-icon>
      <div v-if="label !== null" style="line-height: 1;"
      :style="{
        fontSize: fontSize
      }"
      class="text-center text-bold"
      >{{ label }}</div>
    </div>
  </div>
</template>

<script setup>
import { ref, defineProps, getCurrentInstance } from 'vue'

const { emit } = getCurrentInstance()

const { label, icon, width, height, fontSize, iconSize } = defineProps({
  label: {
    type: String,
    default: ''
  },
  icon: {
    type: String,
    default: 'fa-solid fa-gear'
  },
  width: {
    type: String,
    default: '8vw'
  },
  height: {
    type: String,
    default: '8vw'
  },
  fontSize: {
    type: String,
    default: '2vw'
  },
  iconSize: {
    type: String,
    default: '3vw'
  }
})

const btnRef = ref(null)

const isClick = ref(false)

const onClick = () => {
  emit('confirm')
}

const onMouseDown = () => {
  isClick.value = true
}

const onMouseUp = () => {
  isClick.value = false
}

const onMouseLeave = () => {
  isClick.value = false
}
</script>
