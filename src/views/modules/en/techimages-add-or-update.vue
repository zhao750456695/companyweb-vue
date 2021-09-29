<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="名称" prop="iname">
      <el-input v-model="dataForm.iname" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="描述" prop="descri">
      <el-input v-model="dataForm.descri" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="图片链接" prop="url">
      <el-input v-model="dataForm.url" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="上传日期" prop="updated">
      <el-input v-model="dataForm.updated" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="顺序" prop="iorder">
      <el-input v-model="dataForm.iorder" placeholder=""></el-input>
    </el-form-item>
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">取消</el-button>
      <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
    </span>
  </el-dialog>
</template>

<script>
  export default {
    data () {
      return {
        visible: false,
        dataForm: {
          id: 0,
          iname: '',
          descri: '',
          url: '',
          updated: '',
          iorder: ''
        },
        dataRule: {
          name: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          descri: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          url: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          updated: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          category: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.id = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.id) {
            this.$http({
              url: this.$http.adornUrl(`/generator/techimages/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.iname = data.techimages.iname
                this.dataForm.descri = data.techimages.descri
                this.dataForm.url = data.techimages.url
                this.dataForm.updated = data.techimages.updated
                this.dataForm.iorder = data.techimages.iorder
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
              url: this.$http.adornUrl(`/generator/techimages/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'iname': this.dataForm.iname,
                'descri': this.dataForm.descri,
                'url': this.dataForm.url,
                'updated': this.dataForm.updated,
                'iorder': this.dataForm.iorder
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
