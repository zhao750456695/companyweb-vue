<template>
  <el-dialog
    title="修改"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="标题" prop="title">
      <el-input v-model="dataForm.title" placeholder="ddd"></el-input>
    </el-form-item>
    <el-form-item label="发布日期" prop="date">
          <el-date-picker
            v-model="value"
            type="date"
            placeholder="选择日期"
            >
          </el-date-picker>
    </el-form-item>
    <el-form-item label="描述" prop="descri">
      <el-input type="textarea" :rows="2" :autosize="{ minRows: 6}"  v-model="dataForm.descri" placeholder="描述"></el-input>
    </el-form-item>
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
    <div id="app" style="margin-top: 26px;">
      <h3>内容</h3>
      <ckeditor id="newseditor" v-model="dataForm.content" :config="editorConfig" @ready="onEditorReady"></ckeditor>
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
  // var ed = null
  export default {
    data () {
      return {
        ed: {},
        value: '',
        myHeaders: {token},
        fileList: [],
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
          descri: '',
          content: '',
          id: 0,
          cover: ''
        },
        dataRule: {
          title: [
            { required: true, message: 'ddd不能为空', trigger: 'blur' }
          ],
          updated: [
            { required: true, message: '发布日期不能为空', trigger: 'blur' }
          ],
          author: [
            { required: true, message: '作者不能为空', trigger: 'blur' }
          ],
          descri: [
            { required: true, message: '描述不能为空', trigger: 'blur' }
          ],
          content: [
            { required: true, message: '内容不能为空', trigger: 'blur' }
          ],
          cover: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    components: {
      // Use the <ckeditor> component in this view.
      ckeditor: CKEditor.component
    },
    methods: {
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
      onEditorReady (editor) {
        // vue中获取CKEditor对象的方法
        this.ed = editor
        this.ed.setData(this.dataForm.content)
      },
      init (id) {
        this.dataForm.id = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.id) {
            this.$http({
              url: this.$http.adornUrl(`/generator/shows/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              console.log(data)
              if (data && data.code === 0) {
                this.dataForm.title = data.shows.title
                let d = data.shows.updated
                let date = null
                if (d === 'NaN-NaN-NaN' || d === null || d === undefined) {
                  date = new Date()
                } else {
                  date = new Date(d)
                }
                this.value = date
                this.dataForm.descri = data.shows.descri
                this.dataForm.content = data.shows.content
                this.fileList = []
                data.shows.images.forEach(i => {
                  this.fileList.push({url: i})
                })
              }
            })
          }
        })
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
            let urls = []
            this.fileList.forEach(i => {
              urls.push(i.url)
            })
            console.log('urlsssss', urls)
            this.$http({
              url: this.$http.adornUrl(`/generator/shows/update`),
              method: 'post',
              data: this.$http.adornData({
                'title': this.dataForm.title || undefined,
                'descri': this.dataForm.descri,
                'content': this.ed.getData(),
                'images': urls,
                'id': this.dataForm.id || undefined,
                'updated': this.dateToString(this.value)
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
