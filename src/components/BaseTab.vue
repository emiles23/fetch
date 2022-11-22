<template>
  <div class="flex items-center bg-slate-400 text-3xl uppercase text-white">
    <div @click="currentTab = index" v-for="(tab, index) in tabs" :key="tab" class="w-full text-center py-5 cursor-pointer" :class="[currentTab === index ? 'border-b-4 border-cyan-400 bg-cyan-600' : '']">
      {{tab}}
    </div>
  </div>
</template>

<script>
export default {
  props: ['modelValue'],
  emits: ['update:modelValue'],
  props:{
    tabs: {
      required: true,
      default: []
    }
  },
  data() {
    return {
      currentTab: 0
    }
  },
  watch: {
    currentTab(newValue) {
      this.$emit('update:modelValue', newValue)
    }
  }
}
</script>
