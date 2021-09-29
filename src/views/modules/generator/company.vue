<template>
  <div class="mod-config">
    <div>
      <!-- 首页的轮播图 category为1，简介的轮播图category为2，数据表为tb_webimages -->
      <el-row>
          <el-col :span="24"><h3>简介轮播图</h3></el-col>
      </el-row>
      <el-row>
        <el-row style="margin-bottom: 10px">
                <el-table
                :data="pictureList"
                border
                v-loading="dataListLoading"
                style="width: 100%;">
                <el-table-column
                    prop="id"
                    header-align="center"
                    align="center"
                    v-if="false"
                    label="">
                </el-table-column>
                <el-table-column
                    prop="bigTitle"
                    header-align="center"
                    align="center"
                    label="名称">
                </el-table-column>

                <el-table-column
                    prop="iorder"
                    header-align="center"
                    align="center"
                    label="顺序">
                </el-table-column>
                <el-table-column
                    prop="url"
                    header-align="center"
                    align="center"
                    label="图片">
                    <template width="90" slot-scope="scope">
                        <img style="width:80px;height:80px;border:none;" :src="scope.row.url">
                    </template>
                </el-table-column>
                <el-table-column
                    prop="updated"
                    header-align="center"
                    align="center"
                    label="上传时间">
                </el-table-column>
                <el-table-column
                    fixed="right"
                    header-align="center"
                    align="center"
                    width="150"
                    label="操作">
                    <template slot-scope="scope">
                    <el-button type="text" size="small" @click="UpdateHandle(scope.row.id)">修改</el-button>
                    <el-button type="text" size="small" @click="deleteHandle(scope.row.id)">删除</el-button>
                    </template>
                </el-table-column>
                </el-table>
            </el-row>
            <el-card class="box-card">
                <el-form :model="pdataForm" @keyup.enter.native="pdataFormSubmit()" label-width="80px">
                <el-form-item label="名称" prop="bigTitle">
                <el-input v-model="pdataForm.bigTitle" placeholder=""></el-input>
                </el-form-item>
                <el-form-item label="展示顺序" prop="iorder">
                  <el-input-number v-model="pdataForm.iorder" :min="1" :max="10" label=""></el-input-number>
                </el-form-item>
                <el-upload
                    ref='bp'
                    action="https://jsonplaceholder.typicode.com/posts/"
                    :http-request="uploadPicture"
                    :headers="myHeaders"
                    :limit=1
                    list-type="picture-card"
                    :on-remove="handleRemove">
                    <i class="el-icon-plus"></i>
                </el-upload>
                <h4>图片大小：555*300</h4>
                </el-form>
                <el-row style="margin-top: 10px">
                    <el-button type="success" round @click="pdataFormSubmit()">添加</el-button>
                    <el-button type="warning" round @click="clearall()">清空</el-button>
                </el-row>
            </el-card>
        </el-row>
      </el-row>
      <el-row style="margin-top: 36px;">
          <el-col :span="24"><h3>简介概况</h3></el-col>
      </el-row>
        <el-row>
          <img src="http://weidu-company.oss-cn-beijing.aliyuncs.com/20210911/f9f2b45ee6104903a2407bb3d13aaf83.png" />
        </el-row>
        <el-form :model="dataForm" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
          <el-form-item label="大标题" prop="bigtitle">
            <el-input v-model="dataForm.bigtitle" placeholder=""></el-input>
          </el-form-item>
          <el-form-item label="小标题一" prop="smalltitle1">
            <el-input v-model="dataForm.smalltitle1" placeholder=""></el-input>
          </el-form-item>
          <el-form-item label="内容一" prop="desc1">
            <el-input
                type="textarea"
                :rows="2"
                :autosize="{ minRows: 6}"
                placeholder=""
                v-model="dataForm.desc1">
            </el-input>
          </el-form-item>
          <el-form-item label="小标题二" prop="smalltitle2">
            <el-input v-model="dataForm.smalltitle2" placeholder=""></el-input>
          </el-form-item>
          <el-form-item label="内容二" prop="web">
            <el-input
                type="textarea"
                :rows="2"
                :autosize="{ minRows: 6}"
                placeholder=""
                v-model="dataForm.desc2">
            </el-input>
          </el-form-item>
          <el-form-item label="小标题三" prop="smalltitle3">
            <el-input v-model="dataForm.smalltitle3" placeholder=""></el-input>
          </el-form-item>
          <el-form-item label="内容三" prop="web">
            <el-input
                type="textarea"
                :rows="2"
                :autosize="{ minRows: 6}"
                placeholder=""
                v-model="dataForm.desc3">
            </el-input>
          </el-form-item>
        </el-form>
        <el-row>
            <el-button type="success" round @click="dataFormSubmit()">保存简介概况</el-button>
        </el-row>

        <el-row>
            <el-col :span="24"><h3>简介详情</h3></el-col>
        </el-row>
        <el-row>
          <div id="app">
              <ckeditor id="newseditor" v-model="introDetail" :config="editorConfig"></ckeditor>
          </div>
          <el-button style="margin-top: 36px;" type="success" round @click="saveIntroDetail">保存介绍详情</el-button>
        </el-row>
    </div>
    <add-or-update v-if="addOrUpdateVisible" ref="addOrUpdate" @refreshDataList="getPictureList()"></add-or-update>
  </div>
</template>

<script>
  import AddOrUpdate from './webimages-add-or-update'
  import Vue from 'vue'
  import CKEditor from 'ckeditor4-vue'
  var token = Vue.cookie.get('token')
  export default {
    data () {
      return {
        pictureList: [],
        introPicture: [],
        introDetail: '',
        dataList: [],
        editorData: '<p>Content of the editor.</p>',
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
          bigtitle: '',
          smalltitle1: '',
          smalltitle2: '',
          smalltitle3: '',
          desc1: '',
          desc2: '',
          desc3: ''
        },
        myHeaders: {token},
        pdataForm: {
          bigTitle: '',
          iorder: 1
        },
        dataListLoading: false,
        addOrUpdateVisible: false
      }
    },
    components: {
      AddOrUpdate,
      ckeditor: CKEditor.component
    },
    activated () {
      this.getPictureList()
      this.getDataList()
    },
    methods: {
      // ===================================================轮播图开始
      // 表格操作
      UpdateHandle (id) {
        this.addOrUpdateVisible = true
        this.$nextTick(() => {
          this.$refs.addOrUpdate.init(id)
        })
      },
      deleteHandle (id) {
        // 删除大图
        var imgObj = [id]
        this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          this.$http({
            url: this.$http.adornUrl('/generator/webimages/delete'),
            method: 'post',
            data: imgObj
          }).then(({data}) => {
            this.$message({
              message: '删除成功',
              type: 'success'
            })
            this.getPictureList()
          })
        }).catch(() => {
          this.$message({
            type: 'info',
            message: '已取消删除'
          })
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
          this.introPicture = []
          this.introPicture.push(data)
        })
      },
      // 删除图片
      handleRemove (file, fileList) {
        console.log(file, fileList)
        this.introPicture = fileList
      },
      // 清空
      clearall () {
        this.pdataForm.bigTitle = ''
        this.pdataForm.iorder = null
        this.pictureList = []
        this.$refs.bp.clearFiles()
      },
      // 轮播图的提交
      pdataFormSubmit () {
        if (this.introPicture.length > 0) {
          let goodsObj = {
            bigTitle: this.pdataForm.bigTitle,
            iorder: this.pdataForm.iorder,
            category: 2,
            url: this.introPicture[0]
          }
          console.log(goodsObj)
          this.$http({
            url: this.$http.adornUrl('/generator/webimages/save'),
            method: 'post',
            data: goodsObj
          }).then(({data}) => {
            this.$message({
              message: '添加成功',
              type: 'success'
            })
            this.getPictureList()
            this.clearall()
            this.$refs.bp.clearFiles()
          })
        }
      },
      // 获取轮播图
      getPictureList () {
        this.dataListLoading = true
        this.$http({
          url: this.$http.adornUrl('/generator/webimages/getIntroPictureList'),
          method: 'get'
        }).then(({data}) => {
          console.log(data)
          if (data && data.code === 0) {
            this.pictureList = data.introImages
          } else {
            this.pictureList = []
          }
          this.dataListLoading = false
        })
      },
      // ====================================== 轮播图结束
      // ====================================== 获取数据开始
      getDataList () {
        this.dataListLoading = true
        this.$http({
          url: this.$http.adornUrl('/generator/company/list'),
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
            // 1简介2详情3历程4详情5架构6详情7资质8详情
            for (var i = 0; i < this.dataList.length; i++) {
              if (this.dataList[i].category === 1) {
                this.dataForm = JSON.parse(this.dataList[i].content)
              }
              if (this.dataList[i].category === 2) {
                this.introDetail = this.dataList[i].content
              }
            }
          } else {
            this.dataList = []
            this.totalPage = 0
          }
          this.dataListLoading = false
        })
      },
      dataFormSubmit () {
        var content = {
          'bigtitle': this.dataForm.bigtitle,
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
          category: 1
        }
        this.$http({
          url: this.$http.adornUrl('/generator/company/save'),
          method: 'post',
          data: data
        }).then(({data}) => {
          this.$message({
            message: '保存成功',
            type: 'success'
          })
        })
      },
      saveIntroDetail () {
        var data = {
          'content': this.introDetail,
          'category': 2
        }
        this.$http({
          url: this.$http.adornUrl('/generator/company/save'),
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
