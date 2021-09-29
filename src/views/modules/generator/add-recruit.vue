<template>
<div>
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="标题" prop="title">
      <el-input v-model="dataForm.title" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="作者" prop="author">
      <el-input v-model="dataForm.author" placeholder=""></el-input>
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
      <el-input type="textarea" autosize v-model="dataForm.desc" :rows="10" style="width: 50%;" maxlength="110" show-word-limit placeholder=""></el-input>
    </el-form-item>


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
            console.log(ed.getData())
            this.$http({
              url: this.$http.adornUrl(`/generator/recruit/save`),
              method: 'post',
              data: this.$http.adornData({
                'title': this.dataForm.title || undefined,
                'updated': this.dateToString(this.value),
                'author': this.dataForm.author,
                'descri': this.dataForm.desc,
                'content': ed.getData()
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
        this.dataForm.author = ''
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
