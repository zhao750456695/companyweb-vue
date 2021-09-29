<template>
<div>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="标题" prop="title">
      <el-input v-model="dataForm.title" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="编辑者" prop="author">
      <el-input v-model="dataForm.author" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="简介" prop="desc">
      <el-input type="textarea" autosize v-model="dataForm.desc" placeholder=""></el-input>
    </el-form-item>

    <el-form-item label="封面" prop="cover">
        <el-upload
        action="https://jsonplaceholder.typicode.com/posts/"
        list-type="picture-card"
        :http-request="uploadListPicture"
        :headers="myHeaders"
        :limit="1"
        :before-upload="beforeAvatarUpload"
        :on-preview="handlePictureCardPreview"
        :on-remove="handleRemove1">
        <i class="el-icon-plus"></i>
      </el-upload>
      <el-dialog :visible.sync="dialogVisible">
        <img width="100%" :src="dialogImageUrl" alt="">
      </el-dialog>
    </el-form-item>
  <!-- <div class="mod-demo-ueditor">
    <script :id="ueId" class="ueditor-box" type="text/plain" style="width: 100%; height: 260px;">{{dataForm.content}}</script>
    
  </div> -->

    <div id="app">
      <ckeditor id="newseditor" v-model="editorData" :config="editorConfig" @ready="onEditorReady"></ckeditor>
    </div>

    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">取消</el-button>
      <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
    </span>


  </el-dialog>

</div>  
</template>

<script>
  import Vue from 'vue'
  import CKEditor from 'ckeditor4-vue'
  var token = Vue.cookie.get('token')
  var ed = null
  export default {
    data () {
      return {
        myHeaders: {token},
        listPicture: [],
        editorData: '<p>Content of the editor.</p>',
        editorConfig: {
          height: 500,
          font_names: '宋体/宋体;黑体/黑体;仿宋/仿宋_GB2312;楷体/楷体_GB2312;隶书/隶书;幼圆/幼圆;微软雅黑/微软雅黑;Arial;Times New Roman;Verdana;Comic Sans MS/Comic Sans MS, cursive;Courier New/Courier New, Courier, monospace;Georgia/Georgia, serif;Lucida Sans Unicode/Lucida Sans Unicode, Lucida Grande, sans-serif;Tahoma/Tahoma, Geneva, sans-serif;Trebuchet MS/Trebuchet MS, Helvetica, sans-serif;',
          // filebrowserImageUploadUrl: 'http://192.168.10.88:8898/renren-fast/generator/news/test?token=' + token,
          filebrowserImageUploadUrl: 'http://127.0.0.1:8898/renren-fast/generator/news/test?token=' + token,
          uploadUrl: 'http://127.0.0.1:8898/renren-fast/generator/news/uploadPasteImage?token=' + token,
          // 配置中文
          language: 'zh-cn',
          extraPlugins: 'uploadimage,button,toolbar,notification,clipboard,lineutils,dialogui,dialog,widgetselection,widget,uicolor,font,colorbutton,justify,showblocks'
        },
        visible: false,
        dataForm: {
          title: '',
          updated: '',
          author: '',
          desc: '',
          content: '',
          id: 0
        },
        dataRule: {
          title: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ]
        },
        dialogVisible: false,
        dialogImageUrl: ''
      }
    },
    components: {
      // Use the <ckeditor> component in this view.
      ckeditor: CKEditor.component
    },
    methods: {
      onEditorReady (editor) {
        // vue中获取CKEditor对象的方法
        ed = editor
      },
      uploadListPicture (item) {
        console.log(item.file)
        let fd = new FormData()
        fd.append('file', item.file)
        this.$http({
          url: this.$http.adornUrl('/upload/uploadImage?token=' + this.$cookie.get('token') + '&cat=7'),
          method: 'post',
          data: fd
        }).then(({data}) => {
          item.onSuccess()
          // console.log(data)
          this.listPicture.push(data)
          console.log(this.listPicture)
        })
      },
      beforeAvatarUpload (file) {
        const isLt2M = file.size / 1024 / 1024 < 20

        if (!isLt2M) {
          this.$message.error('上传头像图片大小不能超过 20MB!')
        }
        return isLt2M
      },
      handlePictureCardPreview (file) {
        this.dialogImageUrl = file.url
        this.dialogVisible = true
      },
      handleRemove1 (file, fileList) {
        console.log(file, fileList)
        this.listPicture = []
      },
      init (id) {
        this.dataForm.id = id
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          console.log(this.ue)
          if (this.dataForm.id) {
            this.$http({
              url: this.$http.adornUrl(`/generator/news/en/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.title = data.news.title
                this.dataForm.author = data.news.author
                this.dataForm.desc = data.news.descri
                this.dataForm.content = data.news.content
                // console.log(this.dataForm.content)
                this.editorData = this.dataForm.content
                // this.ue = ueditor.getEditor(this.ueId, {
                //   serverUrl: '/ueditor/upload', // 服务器统一请求接口路径
                //   zIndex: 3000
                // })
                // this.ue.addListener('ready', () => {
                //   this.ue.setContent(this.dataForm.content)
                // })
              }
            })
          } else {
            this.editorData = ''
            console.log('666')
          }
        })
      },
      // 表单提交
      dataFormSubmit () {
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            this.dataForm.content = this.editorData
            console.log(this.dataForm.title)
            console.log(this.dataForm.desc)
            console.log(this.dataForm.author)
            console.log(ed.getData())
            // console.log(CKEditor.component.instance.getData())
            if (!this.dataForm.id) {
              this.$http({
                url: this.$http.adornUrl(`/generator/news/en/save`),
                method: 'post',
                data: this.$http.adornData({
                  'title': this.dataForm.title || undefined,
                  'author': this.dataForm.author,
                  'descri': this.dataForm.desc,
                  'content': ed.getData(),
                  'cover': this.listPicture[0]
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
            } else {
              this.$http({
                url: this.$http.adornUrl(`/generator/news/en/update`),
                method: 'post',
                data: this.$http.adornData({
                  'id': this.dataForm.id,
                  'title': this.dataForm.title || undefined,
                  'author': this.dataForm.author,
                  'descri': this.dataForm.desc,
                  'content': ed.getData(),
                  'cover': this.listPicture[0]
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
          }
        })
      }
    }
  }
</script>
<style lang="scss">
  .mod-demo-ueditor {
    position: relative;
    z-index: 510;
    > .el-alert {
      margin-bottom: 10px;
    }
  }
</style>