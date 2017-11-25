<template>
  <div>
    <el-form label-width="100px"
             :model="model"
             :rule="rules">
      <el-form-item label="姓名"
                    prop="user">
        <el-input v-model="model.user"></el-input>
      </el-form-item>
      <el-form-item label="摘要">
        <el-input v-model="model.summary"></el-input>
      </el-form-item>
      <el-form-item label="内容">
        <el-input type="textarea"
                  v-model="model.content"></el-input>
      </el-form-item>
      <p style="text-align:center">
        <el-button type="primary"
                   @click="submit">提交</el-button>
        <el-button type=""
                   @click="$emit('cancel')">取消</el-button>
      </p>
    </el-form>
  </div>
</template>
<script>
export default {
  props: {
    data: Object // 用于接受修改的值
  },
  data: function () {
    return {
      model: {},
      rules: {
        user: { required: true, message: '请输入姓名', trigger: 'blur' },
        content: { required: true, message: '请输入标题', trigger: 'blur' },
        summary: { required: true, message: '请输入内容', trigger: 'blur' }
      }
    }
  },
  watch: {
    data: function () {
      this.model = this.data
    }
  },
  mounted: function () {
    if (this.data && Object.keys(this.data).length) {
      this.model = this.data
    } else {
      this.model = this.initModel()
    }
  },
  methods: {
    // 初始化model
    initModel: function () {
      return {
        user: '',
        content: '',
        summary: ''
      }
    },
    // 提交表单
    submit: function () {
      console.log('我准备提交的值', this.data)
      this.$emit('submit', this.model)
    },
    // 重置表单
    reset: function () {
      this.model = this.initModel()
    }
  }
}
</script>
<style>

</style>
