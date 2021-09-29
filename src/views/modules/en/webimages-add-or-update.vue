<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="名称" prop="iname">
      <el-input v-model="dataForm.iname" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="分类" prop="category">
      <el-input v-model="dataForm.category" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="图片链接" prop="url">
      <el-input v-model="dataForm.url" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="顺序" prop="iorder">
      <el-input v-model="dataForm.iorder" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="大标题" prop="bigTitle">
      <el-input v-model="dataForm.bigTitle" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="小标题" prop="smallTitle">
      <el-input v-model="dataForm.smallTitle" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="网页链接" prop="link">
      <el-input v-model="dataForm.link" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="更新日期" prop="updated">
      <el-input v-model="dataForm.updated" placeholder=""></el-input>
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
          category: '',
          url: '',
          iorder: '',
          bigTitle: '',
          smallTitle: '',
          link: '',
          updated: ''
        },
        dataRule: {
          iname: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          category: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          url: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          iorder: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          bigTitle: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          smallTitle: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          link: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          updated: [
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
              url: this.$http.adornUrl(`/generator/webimages/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.iname = data.webimages.iname
                this.dataForm.category = data.webimages.category
                this.dataForm.url = data.webimages.url
                this.dataForm.iorder = data.webimages.iorder
                this.dataForm.bigTitle = data.webimages.bigTitle
                this.dataForm.smallTitle = data.webimages.smallTitle
                this.dataForm.link = data.webimages.link
                this.dataForm.updated = data.webimages.updated
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
              url: this.$http.adornUrl(`/generator/webimages/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'iname': this.dataForm.iname,
                'category': this.dataForm.category,
                'url': this.dataForm.url,
                'iorder': this.dataForm.iorder,
                'bigTitle': this.dataForm.bigTitle,
                'smallTitle': this.dataForm.smallTitle,
                'link': this.dataForm.link,
                'updated': this.dataForm.updated
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
