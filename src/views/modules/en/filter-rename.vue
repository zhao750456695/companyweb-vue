<template>
  <el-dialog
    title="分类重命名"
    :close-on-click-modal="false"
    :append-to-body="true"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <!-- <el-form-item label="" prop="parentId">
      <el-input v-model="dataForm.parentId" placeholder=""></el-input>
    </el-form-item> -->
    <el-form-item label="" prop="catName">
      <el-input v-model="dataForm.catName" placeholder="种类名称"></el-input>
    </el-form-item>
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">取消</el-button>
      <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
    </span>
  </el-dialog>
</template>
<style>
.v-modal {
    z-index: 1888;
}
</style>
<script>
  export default {
    data () {
      return {
        visible: false,
        dataForm: {
          catId: 0,
          parentId: '',
          catName: ''
        },
        dataRule: {
          catName: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (data) {
        this.dataForm.catId = data.id || 0
        this.dataForm.parentId = data.parentId
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          this.dataForm.catName = data.label
          // if (this.dataForm.catId) {
          //   this.$http({
          //     url: this.$http.adornUrl(`/generator/goodscategory/info/${this.dataForm.catId}`),
          //     method: 'get',
          //     params: this.$http.adornParams()
          //   }).then(({data}) => {
          //     if (data && data.code === 0) {
          //       this.dataForm.parentId = data.goodsCategory.parentId
          //       this.dataForm.catName = data.goodsCategory.catName
          //     }
          //   })
          // }
        })
      },
      // 表单提交
      dataFormSubmit () {
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            this.$http({
              url: this.$http.adornUrl(`/generator/filter/en/rename`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.catId || undefined,
                'parentId': this.dataForm.parentId,
                'label': this.dataForm.catName
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
