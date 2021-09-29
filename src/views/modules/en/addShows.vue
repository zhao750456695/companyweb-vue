<template>
<div>
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="标题" prop="title">
      <el-input v-model="dataForm.title" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="发布日期" prop="date">
          <el-date-picker
            v-model="value"
            type="date"
            placeholder="选择日期"
            >
          </el-date-picker>
    </el-form-item>
    <el-form-item label="简介" prop="desc">
      <el-input type="textarea" autosize v-model="dataForm.desc" :rows="10" style="width: 50%;" maxlength="140" show-word-limit placeholder=""></el-input>
    </el-form-item>

    <el-form-item label="封面" prop="cover">
<!--        <el-upload
        action="https://jsonplaceholder.typicode.com/posts/"
        list-type="picture-card"
        :http-request="uploadListPicture"
        :headers="myHeaders"
        :limit="1"
        :before-upload="beforeAvatarUpload"
        :on-preview="handlePictureCardPreview"
        :on-remove="handleRemove1">
        <i class="el-icon-plus"></i>
      </el-upload> -->
<!--      <el-dialog :visible.sync="dialogVisible">
        <img width="100%" :src="dialogImageUrl" alt="">
      </el-dialog> -->
      <el-carousel v-if="fileList.length" :interval="2000" style="width: 260px; margin: auto;">
        <el-carousel-item v-for="(item, i) in fileList" :key="i" style="width: 260px; height: 200px;">
          <img style="width: 100%;" :src="item.url" />
        </el-carousel-item>
      </el-carousel>
      <el-upload
        ref="uploadImg"
        class="upload-demo"
        action="https://jsonplaceholder.typicode.com/posts/"
        :http-request="handleUpload"
        :headers="myHeaders"
        :on-remove="handleRemove"
        :file-list="fileList"
        list-type="picture">
        <el-button size="small" type="primary">点击上传</el-button>
        <div slot="tip" class="el-upload__tip">图片尺寸260*200，只能上传jpg/png文件，且不超过1000kb，不超过5张</div>
      </el-upload>
    </el-form-item>
  <!-- <div class="mod-demo-ueditor">
    <script :id="ueId" class="ueditor-box" type="text/plain" style="width: 100%; height: 260px;">{{dataForm.content}}</script>
  </div> -->

    <div id="app">
      <ckeditor id="newseditor" v-model="editorData" :config="editorConfig" @ready="onEditorReady"></ckeditor>
    </div>

    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button type="primary" @click="dataFormSubmit()" style="margin-top: 36px;">保存</el-button>
      <el-button @click="cancelClick" style="margin-left: 16px;">清空</el-button>
    </span>

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
        value: '',
        myHeaders: {token},
        listPicture: [],
        editorData: '',
        editorConfig: {
          height: 500,
          font_names: '宋体/宋体;黑体/黑体;仿宋/仿宋_GB2312;楷体/楷体_GB2312;隶书/隶书;幼圆/幼圆;微软雅黑/微软雅黑;Arial;Times New Roman;Verdana;Comic Sans MS/Comic Sans MS, cursive;Courier New/Courier New, Courier, monospace;Georgia/Georgia, serif;Lucida Sans Unicode/Lucida Sans Unicode, Lucida Grande, sans-serif;Tahoma/Tahoma, Geneva, sans-serif;Trebuchet MS/Trebuchet MS, Helvetica, sans-serif;',
          // filebrowserImageUploadUrl: 'http://192.168.10.88:8898/renren-fast/generator/news/test?token=' + token,
          filebrowserImageUploadUrl: 'http://182.92.201.115:8898/chunjiang/generator/news/test?token=' + token,
          uploadUrl: 'http://182.92.201.115:8898/chunjiang/generator/news/uploadPasteImage?token=' + token,
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
        fileList: []
      }
    },
    components: {
      // Use the <ckeditor> component in this view.
      ckeditor: CKEditor.component
    },
    activated () {
      this.value = new Date()
    },
    methods: {
      onEditorReady (editor) {
        // vue中获取CKEditor对象的方法
        ed = editor
      },
      handleUpload (item) {
        console.log(item.file)
        let fd = new FormData()
        fd.append('file', item.file)
        this.$http({
          url: this.$http.adornUrl('/upload/uploadImage?token=' + this.$cookie.get('token') + '&cat=0'),
          method: 'post',
          data: fd
        }).then(({data}) => {
          this.fileList.push({url: data, name: item.file.name})
          console.log(this.fileList)
        })
      },
      handleRemove (file, fileList) {
        console.log(file, fileList)
        console.log(this.fileList)
        let delId = 0
        this.fileList.forEach(f => {
          if (f.url === file.url) {
            delId = f.id
          }
        })
        console.log('666', delId)
        this.fileList = fileList
        // 从数据库中删除
        // this.$http({
        //   url: this.$http.adornUrl('/generator/sampleimage/show/del'),
        //   method: 'post',
        //   data: delId
        // }).then(({data}) => {
        //   this.$message({
        //     message: '删除成功',
        //     type: 'success'
        //   })
        //   // this.getDataList1()
        // })
      },
      dateToString (date) {
        var year = date.getFullYear()
        var month = (date.getMonth() + 1).toString()
        var day = (date.getDate()).toString()
        if (month.length === 1) {
          month = '0' + month
        }
        if (day.length === 1) {
          day = '0' + day
        }
        var dateTime = year + '-' + month + '-' + day
        return dateTime
      },
      // 表单提交
      dataFormSubmit () {
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            this.dataForm.content = this.editorData
            console.log(this.dataForm.title)
            console.log(this.dataForm.desc)
            console.log(this.dataForm.author)
            console.log(this.dateToString(this.value))
            console.log(this.fileList)
            let urls = []
            this.fileList.forEach(i => {
              urls.push(i.url)
            })
            console.log(ed.getData())
            this.$http({
              url: this.$http.adornUrl(`/generator/news/show/en/save`),
              method: 'post',
              data: this.$http.adornData({
                'title': this.dataForm.title || undefined,
                'updated': this.dateToString(this.value),
                'descri': this.dataForm.desc,
                'content': ed.getData(),
                'images': urls,
                'id': 0
              })
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.$message({
                  message: '操作成功',
                  type: 'success',
                  duration: 1500
                })
                this.cancelClick()
              } else {
                this.$message.error(data.msg)
              }
            })
            // console.log(CKEditor.component.instance.getData())
          }
        })
      },
      cancelClick () {
        this.dataForm.title = ''
        this.value = new Date()
        this.dataForm.desc = ''
        this.fileList = []
        this.editorData = ''
        this.$refs.uploadImg.clearFiles()
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
