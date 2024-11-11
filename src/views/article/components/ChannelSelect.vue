<script setup>
import { artGetChannelsService } from '@/api/article'
import { ref } from 'vue'
const channelList = ref([])
const getChannelList = async () => {
  const res = await artGetChannelsService()
  channelList.value = res.data.data
}
getChannelList()

defineProps({
  width: {
    type: String,
    default: '240px',
  },
})

const modelValue = defineModel()
</script>

<template>
  <el-select
    :modelValue="modelValue"
    @update:modelValue="e => (modelValue = e)"
    :style="{ width }"
  >
    <el-option
      v-for="channel in channelList"
      :key="channel.id"
      :label="channel.cate_name"
      :value="channel.id"
    ></el-option>
  </el-select>
</template>
