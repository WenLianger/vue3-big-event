<script setup>
import { ref } from 'vue'
import ChannelSelect from './components/ChannelSelect.vue'
import ArticleEdit from './components/ArticleEdit.vue'
import { artGetListService } from '@/api/article'
import { formatTime } from '@/utils/format'
const articleList = ref([])
const total = ref(0)
const loading = ref(false)
const drawer = ref()

const onEditArtcle = row => {
  drawer.value.open(row)
}
const onDelArtcle = row => {
  console.log(row)
}
const params = ref({
  pagenum: 1,
  pagesize: 5,
  cate_id: '',
  state: '',
})
const getArticleList = async () => {
  loading.value = true
  const res = await artGetListService(params.value)
  loading.value = false
  articleList.value = res.data.data
  total.value = res.data.total
}
getArticleList()

const onSizeChange = size => {
  console.log(size)
  params.value.pagenum = 1
  params.value.pagesize = size
  getArticleList()
}
const onCurrentChange = page => {
  console.log(page)
  params.value.pagenum = page
  getArticleList()
}
const onSerach = () => {
  params.value.pagenum = 1
  getArticleList()
}
const onReset = () => {
  params.value.pagenum = 1
  params.value.cate_id = ''
  params.value.state = ''
  getArticleList()
}

const onAddArticle = () => {
  console.log(drawer.value)
  drawer.value.open({})
}

const onSuccess = type => {
  if (type === 'add') {
    const lastPage = Math.ceil((total.value + 1) / params.value.pagesize)
    params.value.pagenum = lastPage
  }
  getArticleList()
}
</script>

<template>
  <page-container>
    <template #title>文章管理</template>
    <template #extra>
      <el-button @click="onAddArticle">添加文章</el-button>
    </template>

    <el-form inline>
      <el-form-item label="文章分类：">
        <ChannelSelect v-model="params.cate_id"></ChannelSelect>
      </el-form-item>
      <el-form-item label="发布状态：">
        <el-select v-model="params.state" style="width: 240px">
          <el-option label="已发布" value="已发布"></el-option>
          <el-option label="草稿" value="草稿"></el-option>
        </el-select>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="onSerach">搜索</el-button>
        <el-button @click="onReset">重置</el-button>
      </el-form-item>
    </el-form>

    <el-table :data="articleList" v-loading="loading">
      <el-table-column label="文章标题" prop="title">
        <template #default="{ row }">
          <el-link type="primary" :underline="false">{{ row.title }}</el-link>
        </template>
      </el-table-column>
      <el-table-column label="分类" prop="cate_name"></el-table-column>
      <el-table-column label="发表时间" prop="pub_date">
        <template #default="{ row }">
          {{ formatTime(row.pub_time) }}
        </template>
      </el-table-column>
      <el-table-column label="状态" prop="state"></el-table-column>
      <el-table-column label="操作">
        <template #default="{ row }">
          <el-button @click="onEditArtcle(row)">编辑</el-button>
          <el-button @click="onDelArtcle(row)">删除</el-button>
        </template>
      </el-table-column>
    </el-table>

    <el-pagination
      v-model:current-page="params.pagenum"
      v-model:page-size="params.pagesize"
      :page-sizes="[5, 10, 20]"
      :background="true"
      layout="jumper, total, sizes, prev, pager, next"
      :total="total"
      @size-change="onSizeChange"
      @current-change="onCurrentChange"
    />

    <ArticleEdit ref="drawer" @success="onSuccess"></ArticleEdit>
  </page-container>
</template>

<style scoped></style>
