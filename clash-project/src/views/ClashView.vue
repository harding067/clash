<template>
  <div class="clash-view-container">
    <h1 class="title">{{ title }}</h1>
    <el-row class="clash-url-box">
      <el-col :span="24">
        <div class="url-text-box">
          <el-text class="mx-1 url-text" type="primary">{{ displayUrl }}</el-text>
          <el-icon class="copy-icon" size="small" color="#409eff" @click="copyUrl"
            ><CopyDocument
          /></el-icon>
        </div>
      </el-col>

      <el-col :span="24">
        <el-form ref="rulesFormRef" :model="form" label-width="auto" :rules="rules">
          <el-form-item label="clash url" prop="url">
            <el-input v-model="form.url" placeholder="please input clash url" />
          </el-form-item>
          <el-form-item>
            <el-button type="primary" @click="handleSumbit(rulesFormRef)">update</el-button>
          </el-form-item>
        </el-form>
      </el-col>
    </el-row>
  </div>
</template>

<script lang="ts" setup>
import { ref, onMounted, reactive } from 'vue'
import { ElMessage, type FormInstance, type FormRules } from 'element-plus'

const title = ref('Clash view')

const form = reactive({
  url: '',
})

const rules = reactive<FormRules>({
  url: [{ required: true, message: '请输入url' }],
})

const rulesFormRef = ref<FormInstance | null>(null)

const saveUrl = localStorage.getItem('clashUrl')

const displayUrl = ref('') // 存储用于显示的 URL

onMounted(() => {
  if (saveUrl) {
    displayUrl.value = saveUrl
  }
})

const copyUrl = async () => {
  try {
    await navigator.clipboard.writeText(displayUrl.value)
    ElMessage({
      message: 'URL 已复制到剪贴板',
      type: 'success',
    })
  } catch (err) {
    ElMessage({
      message: '复制失败，请手动复制',
      type: 'error',
    })
  }
}

const handleSumbit = (formEl: FormInstance | null) => {
  if (!formEl) return
  formEl.validate((valid, fields) => {
    if (valid) {
      localStorage.setItem('clashUrl', form.url)
      if (saveUrl) {
        displayUrl.value = form.url
        form.url = ''
        ElMessage({
          message: 'URL 更新成功',
          type: 'success',
        })
      } else {
        ElMessage({
          message: 'URL 更新失败',
          type: 'error',
        })
      }
    } else {
      console.log('error submit!', fields)
    }
  })
}
</script>

<style lang="scss" scoped>
.clash-view-container {
  .title {
    text-align: center;
    margin: 20px 0;
  }

  .clash-url-box {
    .url-text-box {
      margin-bottom: 20px;
    }

    .copy-icon {
      margin-left: 0.5rem;
      cursor: pointer;
    }
  }
}
</style>