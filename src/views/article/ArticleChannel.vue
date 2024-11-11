<script setup>
import { artDelChannelService, artGetChannelsService } from '@/api/article'
import { ref } from 'vue'
import ChannelEdit from './components/ChannelEdit.vue'
import { ElMessage } from 'element-plus'
const channelList = ref([])
const getChannelList = async () => {
  const res = await artGetChannelsService()
  channelList.value = res.data.data
}
getChannelList()

const dialog = ref()
const onAddChannel = () => {
  dialog.value.open({})
}
const onEditChannel = (row, $index) => {
  console.log(row, $index)
  dialog.value.open(row)
}
const onDelChannel = async row => {
  await ElMessageBox.confirm('确认删除？', {
    type: 'warning',
    confirmButtonText: '确认',
    cancelButtonText: '取消',
  })
  await artDelChannelService(row.id)
  ElMessage.success('删除成功')
  getChannelList()
}
</script>

<template>
  <page-container>
    <template #title>文章分类</template>
    <template #extra>
      <el-button @click="onAddChannel">添加分类</el-button>
    </template>

    <el-table :data="channelList">
      <el-table-column type="index" label="序号" width="100"></el-table-column>
      <el-table-column prop="cate_name" label="分类名称"></el-table-column>
      <el-table-column prop="cate_alias" label="分类别名"></el-table-column>
      <el-table-column label="操作" width="160">
        <template #default="{ row, $index }">
          <el-button @click="onEditChannel(row, $index)">编辑</el-button>
          <el-button @click="onDelChannel(row, $index)">删除</el-button>
        </template>
      </el-table-column>

      <template #empty> 表格没有数据！ </template>
    </el-table>

    <channel-edit ref="dialog" @success="getChannelList"></channel-edit>
  </page-container>
</template>

<style scoped></style>
