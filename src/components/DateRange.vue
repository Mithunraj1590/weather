<template>
    <div class="demo-date-picker">
      <div class="w-full ">
        <el-date-picker
          v-model="value2"
          type="daterange"
          unlink-panels
          range-separator="To"
          start-placeholder="Start date"
          end-placeholder="End date"
          :shortcuts="shortcuts"
          :size="size"
          @change="handleDateChange"
        />
      </div>
    </div>
  </template>
  
  <script setup>
  import { ref, defineEmits, onMounted } from 'vue'
  
  const size = ref('default')
  const value2 = ref([new Date(), new Date()])
  
  const shortcuts = [
    {
      text: 'Today',
      value: [new Date(), new Date()],
    },
    {
      text: 'Yesterday',
      value: () => {
        const end = new Date()
        const start = new Date()
        start.setDate(start.getDate() - 1)
        return [start, end]
      },
    },
    {
      text: 'A week ago',
      value: () => {
        const end = new Date()
        const start = new Date()
        start.setDate(start.getDate() - 7)
        return [start, end]
      },
    },
    {
      text: 'Last month',
      value: () => {
        const end = new Date()
        const start = new Date()
        start.setMonth(start.getMonth() - 1)
        return [start, end]
      },
    },
  ]
  
  const emit = defineEmits(['date-selected'])
  
  const handleDateChange = (dates) => {
    emit('date-selected', dates)
  }
  
  onMounted(() => {
    const today = [new Date(), new Date()]
    value2.value = today
    handleDateChange(today)
  })
  </script>
  
  
  
  <style>
.demo-date-picker .el-input__wrapper {
  @apply shadow-none hover:shadow-none bg-transparent max-w-[200px] p-0 m-auto;
}
.demo-date-picker .el-input__icon{
  @apply hidden
}
.el-range-input,.el-range-separator{
  @apply !text-[#FF885B] font-semibold
}
  </style>
  