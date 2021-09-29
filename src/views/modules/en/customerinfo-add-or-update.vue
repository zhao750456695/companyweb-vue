<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '咨询详情'"
    :close-on-click-modal="false"
    :before-close="handleClose"
    :visible.sync="visible">
    <el-form :model="dataForm" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="姓名" prop="name">
      <el-input :disabled="true" v-model="dataForm.name" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="邮箱" prop="email">
      <el-input :disabled="true" v-model="dataForm.email" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="电话" prop="phone">
      <el-input :disabled="true" v-model="dataForm.phone" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="内容" prop="content">
      <el-input :disabled="true" type="textarea" :autosize="{ minRows: 6, maxRows: 20}" v-model="dataForm.content" placeholder=""></el-input>
    </el-form-item>
    <h3>请通过下面开关更改状态</h3>
      <el-switch
        v-model="readvalue"
        active-color="#13ce66"
        inactive-color="gray"
        active-text="已读"
        inactive-text="未读"
        >
      </el-switch>
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="closeCI">关闭</el-button>
    </span>
  </el-dialog>
</template>

<script>
  export default {
    data () {
      return {
        visible: false,
        readvalue: false,
        dataForm: {
          id: 0,
          name: '',
          email: '',
          phone: '',
          content: ''
        }
      }
    },
    methods: {
      handleClose () {
        this.dataFormSubmit()
      },
      closeCI () {
        this.dataFormSubmit()
        this.visible = false
      },
      init (id) {
        this.dataForm.id = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.id) {
            this.$http({
              url: this.$http.adornUrl(`/generator/customerinfo/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.name = data.customerinfo.name
                this.dataForm.email = data.customerinfo.email
                this.dataForm.phone = data.customerinfo.phone
                this.dataForm.content = data.customerinfo.content
              }
            })
          }
        })
      },
      // 表单提交
      dataFormSubmit () {
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            this.$http({
              url: this.$http.adornUrl(`/generator/customerinfo/update`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id,
                'isread': this.readvalue
              })
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.$message({
                  message: '操作成功',
                  type: 'success',
                  duration: 1500,
                  onClose: () => {
                    this.visible = false
                    this.$emit('refreshDataList')
                  }
                })
              } else {
                this.$message.error(data.msg)
              }
            })
          }
        })
      }
    }
  }
</script>
