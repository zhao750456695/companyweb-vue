<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="标题" prop="title">
      <el-input v-model="dataForm.title" placeholder="标题"></el-input>
    </el-form-item>
    <el-form-item label="发布日期" prop="date">
          <el-date-picker
            v-model="value"
            type="date"
            placeholder="选择日期"
            >
          </el-date-picker>
    </el-form-item>
    <div id="app">
      <ckeditor id="newseditor" v-model="editorData" :config="editorConfig" @ready="onEditorReady"></ckeditor>
    </div>
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">取消</el-button>
      <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
    </span>
  </el-dialog>
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
        editorData: '<p>Content of the editor.</p>',
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
          content: '',
          id: 0
        },
        dataRule: {
          title: [
            { required: true, message: '标题不能为空', trigger: 'blur' }
          ],
          updated: [
            { required: true, message: '发布日期不能为空', trigger: 'blur' }
          ],
          content: [
            { required: true, message: '内容不能为空', trigger: 'blur' }
          ]
        }
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
      init (id) {
        this.dataForm.id = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.id) {
            this.$http({
              url: this.$http.adornUrl(`/generator/info/en/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.title = data.info.title
                this.dataForm.content = data.info.content
                let d = data.info.updated
                let date = new Date(d)
                this.value = date
                this.editorData = this.dataForm.content
              }
            })
          } else {
            this.editorData = ''
            this.value = new Date()
            this.dateToString(this.value)
            console.log('666')
          }
        })
      },
      // 表单提交
      dataFormSubmit () {
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            this.$http({
              url: this.$http.adornUrl(`/generator/info/${!this.dataForm.id ? 'en/save' : 'en/update'}`),
              method: 'post',
              data: this.$http.adornData({
                'title': this.dataForm.title,
                'updated': this.dateToString(this.value),
                'content': ed.getData(),
                'id': this.dataForm.id || undefined
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
