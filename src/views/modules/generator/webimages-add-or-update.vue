<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="名称" prop="bigTitle">
      <el-input v-model="dataForm.bigTitle" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="顺序" prop="iorder">
      <el-input v-model="dataForm.iorder" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="图片" prop="updated">
      <el-upload
          ref='bp'
          action="https://jsonplaceholder.typicode.com/posts/"
          :http-request="uploadPicture"
          :headers="myHeaders"
          :file-list="introPicture"
          :limit=1
          list-type="picture-card"
          :on-remove="handleRemove">
          <i class="el-icon-plus"></i>
      </el-upload>
    </el-form-item>
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">取消</el-button>
      <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
    </span>
  </el-dialog>
</template>

<script>
  import Vue from 'vue'
  var token = Vue.cookie.get('token')
  export default {
    data () {
      return {
        catId: 0,
        introPicture: [],
        visible: false,
        myHeaders: {token},
        dataForm: {
          id: 0,
          category: '',
          iorder: '',
          bigTitle: ''
        },
        dataRule: {
          iorder: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          bigTitle: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      // ==================图片上传
      // 上传图片
      uploadPicture (item) {
        console.log(item.file)
        let fd = new FormData()
        fd.append('file', item.file)
        this.$http({
          url: this.$http.adornUrl('/upload/uploadImage?token=' + this.$cookie.get('token') + '&cat=2'),
          method: 'post',
          data: fd
        }).then(({data}) => {
          item.onSuccess()
          this.introPicture = []
          this.introPicture.push({url: data})
        })
      },
      // 删除图片
      handleRemove (file, fileList) {
        console.log(file, fileList)
        this.introPicture = fileList
      },
      // =========================
      init (id) {
        this.introPicture = []
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
                this.dataForm.id = data.webimages.id
                this.catId = data.webimages.category
                this.introPicture.push({url: data.webimages.url})
                this.dataForm.iorder = data.webimages.iorder
                this.dataForm.bigTitle = data.webimages.bigTitle
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
              url: this.$http.adornUrl(`/generator/webimages/update`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'iname': this.dataForm.iname,
                'category': this.catId,
                'url': this.introPicture[0].url,
                'iorder': this.dataForm.iorder,
                'bigTitle': this.dataForm.bigTitle
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
