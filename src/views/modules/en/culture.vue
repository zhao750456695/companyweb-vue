<template>
  <div class="mod-config">
    <div>
        <el-row>
            <el-col :span="24"><h3>企业文化概况</h3></el-col>
        </el-row>

        <el-row>
          <img src="http://weidu-company.oss-cn-beijing.aliyuncs.com/20210507/ef30cb6a3ee4453281208d2285b2a6d4.png"/>
        </el-row>
        <el-form :model="dataForm" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
        <el-form-item label="小标题一" prop="smalltitle1">
        <el-input v-model="dataForm.smalltitle1" placeholder=""></el-input>
        </el-form-item>
        <el-form-item label="描述一" prop="desc1">
        <el-input v-model="dataForm.desc1" placeholder=""></el-input>
        </el-form-item>
        <el-form-item label="小标题二" prop="smalltitle2">
        <el-input v-model="dataForm.smalltitle2" placeholder=""></el-input>
        </el-form-item>
        <el-form-item label="描述二" prop="web">
        <el-input v-model="dataForm.desc2" placeholder=""></el-input>
        </el-form-item>
        <el-form-item label="小标题三" prop="smalltitle3">
        <el-input v-model="dataForm.smalltitle3" placeholder=""></el-input>
        </el-form-item>
        <el-form-item label="描述三" prop="web">
        <el-input v-model="dataForm.desc3" placeholder=""></el-input>
        </el-form-item>
        </el-form>
        <div style="margin-top: 36px;">
          <el-button type="success" round @click="dataFormSubmit()">保存</el-button>
        </div>

        <el-row>
          <img src="http://weidu-company.oss-cn-beijing.aliyuncs.com/20210911/24d4023e49ec41468f5dcfd59de8f825.png" />
        </el-row>
        <el-form :model="dataForm1" ref="dataForm1" @keyup.enter.native="dataFormSubmit1()" label-width="80px">
        <el-form-item label="大标题" prop="bigtitle">
        <el-input v-model="dataForm1.bigtitle" placeholder=""></el-input>
        </el-form-item>
        <el-form-item label="小标题" prop="smalltitle1">
        <el-input v-model="dataForm1.smalltitle" placeholder=""></el-input>
        </el-form-item>
        <el-form-item label="内容" prop="desc1">
          <el-input
              type="textarea"
              :rows="2"
              :autosize="{ minRows: 6}"
              placeholder=""
              v-model="dataForm1.desc">
          </el-input>
        </el-form-item>
        <el-form-item label="图片" prop="desc1">
          <el-upload
              ref='bp'
              action="https://jsonplaceholder.typicode.com/posts/"
              :http-request="uploadPicture"
              :headers="myHeaders"
              :limit=1
              :file-list="culturePicture"
              list-type="picture-card"
              :on-remove="handleRemove">
              <i class="el-icon-plus"></i>
          </el-upload>
          <h4>图片大小：555*300</h4>
        </el-form-item>
        </el-form>
        <div style="margin-top: 36px;">
          <el-button type="success" round @click="dataFormSubmit1()">保存</el-button>
        </div>
        <el-row>
            <el-col :span="24"><h3>企业文化详情</h3></el-col>
        </el-row>
        <el-row>
          <div id="app">
              <ckeditor v-model="cultureDetail" :config="editorConfig"></ckeditor>
          </div>
          <el-button type="success" round @click="saveCultureDetail">保存详情</el-button>
        </el-row>
    </div>

    <!-- <add-or-update v-if="addOrUpdateVisible" ref="addOrUpdate" @refreshDataList="getDataList"></add-or-update> -->
  </div>
</template>

<script>
  import AddOrUpdate from './news-add-or-update'
  import Vue from 'vue'
  import CKEditor from 'ckeditor4-vue'
  var token = Vue.cookie.get('token')
  export default {
    data () {
      return {
        culturePicture: [],
        myHeaders: {token},
        cultureDetail: '',
        editorConfig: {
          height: 600,
          font_names: '宋体/宋体;黑体/黑体;仿宋/仿宋_GB2312;楷体/楷体_GB2312;隶书/隶书;幼圆/幼圆;微软雅黑/微软雅黑;Arial;Times New Roman;Verdana;Comic Sans MS/Comic Sans MS, cursive;Courier New/Courier New, Courier, monospace;Georgia/Georgia, serif;Lucida Sans Unicode/Lucida Sans Unicode, Lucida Grande, sans-serif;Tahoma/Tahoma, Geneva, sans-serif;Trebuchet MS/Trebuchet MS, Helvetica, sans-serif;',
          filebrowserImageUploadUrl: 'http://182.92.201.115:8898/chunjiang/generator/news/test?token=' + token,
          uploadUrl: 'http://182.92.201.115:8898/chunjiang/generator/news/uploadPasteImage?token=' + token,
          // 配置中文
          language: 'zh-cn',
          extraPlugins: 'button,toolbar,notification,clipboard,lineutils,dialogui,dialog,widgetselection,widget,uicolor,font,colorbutton,justify,showblocks'
        },
        dataForm: {
          smalltitle1: '',
          desc1: '',
          smalltitle2: '',
          desc2: '',
          smalltitle3: '',
          desc3: ''
        },
        dataForm1: {
          bigtitle: '',
          smalltitle: '',
          desc: ''
        },
        textarea: '',
        dataList: [],
        pageIndex: 1,
        pageSize: 10,
        totalPage: 0,
        dataListLoading: false,
        dataListSelections: [],
        addOrUpdateVisible: false
      }
    },
    components: {
      AddOrUpdate,
      ckeditor: CKEditor.component
    },
    activated () {
      this.getDataList()
    },
    methods: {
      getDataList () {
        this.dataListLoading = true
        this.$http({
          url: this.$http.adornUrl('/generator/company/en/list'),
          method: 'get',
          params: this.$http.adornParams({
            'page': this.pageIndex,
            'limit': this.pageSize,
            'key': this.dataForm.key
          })
        }).then(({data}) => {
          if (data && data.code === 0) {
            this.dataList = data.page.list
            this.totalPage = data.page.totalCount
            console.log(this.dataList)
            for (var i = 0; i < this.dataList.length; i++) {
              if (this.dataList[i].category === 3) {
                this.dataForm = JSON.parse(this.dataList[i].content)
              }
              if (this.dataList[i].category === 4) {
                // console.log('444', this.dataList[i].content)
                this.dataForm1 = JSON.parse(this.dataList[i].content)
                this.culturePicture = []
                this.culturePicture.push({url: JSON.parse(this.dataList[i].content).url})
              }
              if (this.dataList[i].category === 5) {
                this.cultureDetail = this.dataList[i].content
              }
            }
          } else {
            this.dataList = []
            this.totalPage = 0
          }
          this.dataListLoading = false
        })
      },
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
          this.culturePicture = []
          this.culturePicture.push({url: data})
        })
      },
      // 删除图片
      handleRemove (file, fileList) {
        console.log(file, fileList)
        this.culturePicture = fileList
      },
      dataFormSubmit1 () {
        console.log(this.culturePicture)
        var content = {
          'bigtitle': this.dataForm1.bigtitle,
          'smalltitle': this.dataForm1.smalltitle,
          'desc': this.dataForm1.desc,
          'url': this.culturePicture[0].url
        }
        content = JSON.stringify(content)
        var data = {
          content: content,
          category: 4
        }
        this.$http({
          url: this.$http.adornUrl('/generator/company/en/save'),
          method: 'post',
          data: data
        }).then(({data}) => {
          this.$message({
            message: '保存成功',
            type: 'success'
          })
        })
      },
      dataFormSubmit () {
        var content = {
          'smalltitle1': this.dataForm.smalltitle1,
          'desc1': this.dataForm.desc1,
          'smalltitle2': this.dataForm.smalltitle2,
          'desc2': this.dataForm.desc2,
          'smalltitle3': this.dataForm.smalltitle3,
          'desc3': this.dataForm.desc3
        }
        content = JSON.stringify(content)
        var data = {
          content: content,
          category: 3
        }
        this.$http({
          url: this.$http.adornUrl('/generator/company/en/save'),
          method: 'post',
          data: data
        }).then(({data}) => {
          this.$message({
            message: '保存成功',
            type: 'success'
          })
        })
      },
      saveCultureDetail () {
        var data = {
          'content': this.cultureDetail,
          'category': 5
        }
        this.$http({
          url: this.$http.adornUrl('/generator/company/en/save'),
          method: 'post',
          data: data
        }).then(({data}) => {
          this.$message({
            message: '保存成功',
            type: 'success'
          })
        })
      }
    }
  }
</script>
