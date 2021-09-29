<template>
  <div class="mod-config">

    <div>
      <el-row>
        <img src="http://weidu-company.oss-cn-beijing.aliyuncs.com/20210913/5687858c1e184681af72ce849ca65550.png" />
      </el-row>
        <el-form :model="dataForm" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
          <el-form-item label="大标题" prop="bigtitle">
            <el-input v-model="dataForm.bigtitle" placeholder=""></el-input>
          </el-form-item>
          <el-form-item label="小标题" prop="smalltitle">
            <el-input v-model="dataForm.smalltitle" placeholder=""></el-input>
          </el-form-item>
          <el-form-item label="内容" prop="desc">
            <el-input
                type="textarea"
                :rows="2"
                :autosize="{ minRows: 6}"
                placeholder=""
                v-model="dataForm.desc">
            </el-input>
          </el-form-item>
        </el-form>
        <el-row>
            <el-button type="success" round @click="dataFormSubmit()">保存</el-button>
        </el-row>
        <el-row>
          <h3>图片</h3>
          <el-card class="box-card">
            <el-upload
                action="https://jsonplaceholder.typicode.com/posts/"
                :http-request="uploadLogoPicture"
                :headers="myHeaders"
                list-type="picture-card"
                :on-preview="handlePictureCardPreview"
                :before-upload="beforeAvatarUpload"
                :file-list="logoList"
                :limit="1"
                :on-remove="handleLogoRemove">
                <i class="el-icon-plus"></i>
            </el-upload>
            <el-dialog :visible.sync="dialogVisible">
                <img width="100%" :src="dialogImageUrl" alt="">
            </el-dialog>
          </el-card>
        </el-row>
        <el-row>
            <el-card class="box-card" style="margin-top: 26px;">
                <el-form :model="dataForm1" ref="dataForm1" @keyup.enter.native="dataFormSubmit1()" label-width="80px">

                <img src="http://weidu-company.oss-cn-beijing.aliyuncs.com/20210514/cd4b0940d40641e493310bf187662227.png">
                <el-form-item label="">
                  <el-row>
                    <el-input-number style="width:20%" v-model="num1" :min="1" :max="100000" label="描述文字"></el-input-number>
                    <el-input style="width:60%" v-model="dataForm1.keyword1" placeholder=""></el-input>
                  </el-row>

                  <el-row>
                    <el-input-number style="width:20%" v-model="num2" :min="1" :max="100000" label="描述文字"></el-input-number>
                    <el-input style="width:60%" v-model="dataForm1.keyword2" placeholder=""></el-input>
                  </el-row>
                  <el-row>
                    <el-input-number style="width:20%" v-model="num3" :min="1" :max="100000" label="描述文字"></el-input-number>
                    <el-input style="width:60%" v-model="dataForm1.keyword3" placeholder=""></el-input>
                  </el-row>
                  <el-row>
                    <el-input-number style="width:20%" v-model="num4" :min="1" :max="100000" label="描述文字"></el-input-number>
                    <el-input style="width:60%" v-model="dataForm1.keyword4" placeholder=""></el-input>
                  </el-row>
                </el-form-item>

                </el-form>
                <el-button type="success" round @click="dataFormSubmit1()">保存</el-button>
                <el-button type="warning" round @click="delWebBase1()">清空</el-button>
                <h3>背景图片</h3>
                <el-upload
                    action="https://jsonplaceholder.typicode.com/posts/"
                    :http-request="uploadBannerPicture"
                    :headers="myHeaders"
                    list-type="picture-card"
                    :on-preview="handlePictureCardPreview"
                    :file-list="bannerList"
                    :limit="1"
                    :on-remove="handleBannerRemove">
                    <i class="el-icon-plus"></i>
                </el-upload>
                <el-dialog :visible.sync="dialogVisible">
                    <img width="100%" :src="dialogImageUrl" alt="">
                </el-dialog>
            </el-card>
        </el-row>
    </div>
  </div>
</template>

<script>
  import Vue from 'vue'
  var token = Vue.cookie.get('token')
  export default {
    data () {
      return {
        logoList: [],
        bannerList: [],
        dataForm1: {
          'abouth1': '',
          'abouth2': '',
          'c1h1': '',
          'c1h2': '',
          'c2h1': '',
          'c2h2': '',
          'c3h1': '',
          'c3h2': '',
          'c4h1': '',
          'c4h2': '',
          'c5h1': '',
          'c5h2': '',
          'c6h1': '',
          'c6h2': '',
          'keyword1': '',
          'keyword2': '',
          'keyword3': '',
          'keyword4': '',
          'newsh1': '',
          'newsh2': '',
          'producth1': '',
          'producth2': '',
          'techh1': '',
          'techh2': ''
        },
        dataForm: {
          bigtitle: '',
          smalltitle: '',
          desc: ''
        },
        myHeaders: {token},
        dataList: [],
        pageIndex: 1,
        pageSize: 10,
        totalPage: 0,
        dataListLoading: false,
        iconurl: '',
        fileList: [],
        configId: 0,
        num1: 0,
        num2: 0,
        num3: 0,
        num4: 0,
        dialogVisible: false,
        dialogImageUrl: ''
      }
    },
    activated () {
      this.fileList = []
      this.getDataList()
      this.getDataList1()
      this.getLogoPicture()
      // this.getBannerPicture()
    },
    methods: {
      uploadBigPicture (item) {
        console.log(item.file)
        let fd = new FormData()
        fd.append('file', item.file)
        this.$http({
          url: this.$http.adornUrl('/upload/uploadImage?token=' + this.$cookie.get('token') + '&cat=3'),
          method: 'post',
          data: fd
        }).then(({data}) => {
          item.onSuccess()
          this.iconurl = data
          console.log('data', data)
        })
      },
      getDataList () {
        this.$http({
          url: this.$http.adornUrl('/generator/index/list'),
          method: 'get',
          params: this.$http.adornParams({
            'page': this.pageIndex,
            'limit': this.pageSize,
            'key': this.dataForm.key
          })
        }).then(({data}) => {
          if (data && data.code === 0) {
            console.log('getDataDDDDDDDD', data)
            this.dataForm1.abouth1 = data.abouth1
            this.dataForm1.abouth2 = data.abouth2
            this.dataForm1.c1h1 = data.aboutlist[0].h1
            this.dataForm1.c1h2 = data.aboutlist[0].h2
            this.dataForm1.c2h1 = data.aboutlist[1].h1
            this.dataForm1.c2h2 = data.aboutlist[1].h2
            this.dataForm1.c3h1 = data.aboutlist[2].h1
            this.dataForm1.c3h2 = data.aboutlist[2].h2
            this.dataForm1.c4h1 = data.aboutlist[3].h1
            this.dataForm1.c4h2 = data.aboutlist[3].h2
            this.dataForm1.c5h1 = data.aboutlist[4].h1
            this.dataForm1.c5h2 = data.aboutlist[4].h2
            this.dataForm1.c6h1 = data.aboutlist[5].h1
            this.dataForm1.c6h2 = data.aboutlist[5].h2
            if (data.aboutlist1[0].h1 !== '') {
              this.num1 = Number(data.aboutlist1[0].h1)
            }
            if (data.aboutlist1[1].h1 !== '') {
              this.num2 = Number(data.aboutlist1[1].h1)
            }
            if (data.aboutlist1[2].h1 !== '') {
              this.num3 = Number(data.aboutlist1[2].h1)
            }
            if (data.aboutlist1[3].h1 !== '') {
              this.num4 = Number(data.aboutlist1[3].h1)
            }
            this.dataForm1.keyword1 = data.aboutlist1[0].h2
            this.dataForm1.keyword2 = data.aboutlist1[1].h2
            this.dataForm1.keyword3 = data.aboutlist1[2].h2
            this.dataForm1.keyword4 = data.aboutlist1[3].h2
            this.dataForm1.newsh1 = data.dynamic.h1
            this.dataForm1.newsh2 = data.dynamic.h2
            this.dataForm1.producth1 = data.product.h1
            this.dataForm1.producth2 = data.product.h2
            this.dataForm1.techh1 = data.tech.h1
            this.dataForm1.techh2 = data.tech.h2
          } else {
            this.dataList = []
            this.totalPage = 0
          }
          this.dataListLoading = false
        })
      },
      getDataList1 () {
        this.dataListLoading = true
        this.$http({
          url: this.$http.adornUrl('/generator/company/list'),
          method: 'get',
          params: this.$http.adornParams({
            'page': 1,
            'limit': 100
          })
        }).then(({data}) => {
          console.log(data, '888888')
          if (data && data.code === 0) {
            // this.dataList = data.page.list
            // this.totalPage = data.page.totalCount
            // console.log(this.dataList)
            // 1简介2详情3历程4详情5架构6详情7资质8详情
            for (var i = 0; i < data.page.list.length; i++) {
              if (data.page.list[i].category === 16) {
                this.dataForm = JSON.parse(data.page.list[i].content)
                console.log('this.dataForm', this.dataForm)
              }
            }
          }
          this.dataListLoading = false
        })
      },
      handleRemove3 (file, fileList) {
        console.log(file, fileList)
        this.bigPicture = fileList
      },
      handlePictureCardPreview (file) {
        this.dialogImageUrl = file.url
        this.dialogVisible = true
      },
      beforeAvatarUpload (file) {
        const isLt2M = file.size / 1024 / 1024 < 20

        if (!isLt2M) {
          this.$message.error('上传图片大小不能超过 5MB!')
        }
        return isLt2M
      },
      delWebBase () {
        var data = [this.configId]
        this.$http({
          url: this.$http.adornUrl('/generator/webase/delete'),
          method: 'post',
          data: data
        }).then(({data}) => {
          this.$message({
            message: '删除成功',
            type: 'success'
          })
        })
        this.dataForm = {
          webname: '',
          keyword: '',
          title: '',
          descri: ''
        }
        this.fileList = []
        this.configId = 0
        this.iconurl = ''
      },
      dataFormSubmit () {
        var content = {
          'bigtitle': this.dataForm.bigtitle,
          'smalltitle': this.dataForm.smalltitle,
          'desc': this.dataForm.desc
        }
        content = JSON.stringify(content)
        var data = {
          content: content,
          category: 16
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
      dataFormSubmit1 () {
        if (this.num1 === undefined) {
          this.num1 = 0
        }
        if (this.num2 === undefined) {
          this.num2 = 0
        }
        if (this.num3 === undefined) {
          this.num3 = 0
        }
        var data = {
          'abouth1': this.dataForm1.abouth1,
          'abouth2': this.dataForm1.abouth2,
          'c1h1': this.dataForm1.c1h1,
          'c1h2': this.dataForm1.c1h2,
          'c2h1': this.dataForm1.c2h1,
          'c2h2': this.dataForm1.c2h2,
          'c3h1': this.dataForm1.c3h1,
          'c3h2': this.dataForm1.c3h2,
          'c4h1': this.dataForm1.c4h1,
          'c4h2': this.dataForm1.c4h2,
          'c5h1': this.dataForm1.c5h1,
          'c5h2': this.dataForm1.c5h2,
          'c6h1': this.dataForm1.c6h1,
          'c6h2': this.dataForm1.c6h2,
          'num1': this.num1,
          'keyword1': this.dataForm1.keyword1,
          'num2': this.num2,
          'keyword2': this.dataForm1.keyword2,
          'num3': this.num3,
          'keyword3': this.dataForm1.keyword3,
          'num4': this.num4,
          'keyword4': this.dataForm1.keyword4,
          'newsh1': this.dataForm1.newsh1,
          'newsh2': this.dataForm1.newsh2,
          'producth1': this.dataForm1.producth1,
          'producth2': this.dataForm1.producth2,
          'techh1': this.dataForm1.techh1,
          'techh2': this.dataForm1.techh2
        }
        this.$http({
          url: this.$http.adornUrl('/generator/index/save'),
          method: 'post',
          data: data
        }).then(({data}) => {
          this.$message({
            message: '保存成功',
            type: 'success'
          })
        })
        console.log(data)
      },
      // =========================================== logo
      // 上传logo图片 sampleimage表，category为8
      uploadLogoPicture (item) {
        console.log(item.file)
        let fd = new FormData()
        fd.append('file', item.file)
        this.$http({
          url: this.$http.adornUrl('/upload/uploadImage?token=' + this.$cookie.get('token') + '&cat=3'),
          method: 'post',
          data: fd
        }).then(({data}) => {
          item.onSuccess()
          this.logourl = data
          console.log('data', data)
          this.saveLogoPicture(this.logourl)
        })
      },
      uploadBannerPicture (item) {
        console.log(item.file)
        let fd = new FormData()
        fd.append('file', item.file)
        this.$http({
          url: this.$http.adornUrl('/upload/uploadImage?token=' + this.$cookie.get('token') + '&cat=3'),
          method: 'post',
          data: fd
        }).then(({data}) => {
          item.onSuccess()
          // this.bannerurl = data
          console.log('data', data)
          this.saveBannerPicture(data)
        })
      },
      // 保存logo图片
      saveLogoPicture (url) {
        var imgObj = {
          url: url,
          category: 16
        }
        this.$http({
          url: this.$http.adornUrl('/generator/sampleimage/save'),
          method: 'post',
          data: imgObj
        }).then(({data}) => {
          this.$message({
            message: '保存成功',
            type: 'success'
          })
          // this.getDataList1()
        })
      },
      saveBannerPicture (url) {
        var imgObj = {
          url: url,
          category: 18
        }
        this.$http({
          url: this.$http.adornUrl('/generator/sampleimage/save'),
          method: 'post',
          data: imgObj
        }).then(({data}) => {
          this.$message({
            message: '保存成功',
            type: 'success'
          })
          // this.getDataList1()
        })
      },
      // 获取logo图片和数字背景图片
      getLogoPicture () {
        this.dataListLoading = true
        this.$http({
          url: this.$http.adornUrl('/generator/sampleimage/list'),
          method: 'get',
          params: this.$http.adornParams({
            'page': 1,
            'limit': 100
          })
        }).then(({data}) => {
          if (data && data.code === 0) {
            this.dataList1 = data.page.list
            this.logoList = []
            this.bannerList = []
            console.log('ddd', this.dataList1)
            for (var i = 0; i < this.dataList1.length; i++) {
              if (this.dataList1[i].category === 16) {
                this.logoList.push({url: this.dataList1[i].url})
                this.logourl = this.dataList1[i].url
              }
              if (data.page.list[i].category === 18) {
                this.bannerList.push({url: data.page.list[i].url})
                // this.logourl = this.dataList1[i].url
              }
            }
          } else {
            this.dataList1 = []
            this.totalPage1 = 0
          }
          this.dataListLoading = false
        })
      },
      // 获取banner图片
      getBannerPicture () {
        this.dataListLoading = true
        this.$http({
          url: this.$http.adornUrl('/generator/sampleimage/list'),
          method: 'get',
          params: this.$http.adornParams({
            'page': 1,
            'limit': 100
          })
        }).then(({data}) => {
          if (data && data.code === 0) {
            // this.dataList1 = data.page.list
            this.bannerList = []
            console.log('bbbbbb', data.page.list)
            for (var i = 0; i < data.page.list.length; i++) {
              if (data.page.list[i].category === 18) {
                this.bannerList.push({url: data.page.list[i].url})
                // this.logourl = this.dataList1[i].url
              }
            }
          } else {
            this.dataList1 = []
            this.totalPage1 = 0
          }
          this.dataListLoading = false
        })
      },
      handleLogoRemove (file, fileList) {
        console.log(file, fileList)
        this.logoList = fileList
        // 删除
        this.$http({
          url: this.$http.adornUrl('/generator/sampleimage/deleteByCat'),
          method: 'post',
          data: 16
        }).then(({data}) => {
          if (data && data.code === 0) {
            this.$message({
              message: '删除成功',
              type: 'success'
            })
          }
        })
      },
      handleBannerRemove (file, fileList) {
        console.log(file, fileList)
        this.logoList = fileList
        // 删除
        this.$http({
          url: this.$http.adornUrl('/generator/sampleimage/deleteByCat'),
          method: 'post',
          data: 18
        }).then(({data}) => {
          if (data && data.code === 0) {
            this.$message({
              message: '删除成功',
              type: 'success'
            })
          }
        })
      }
      // =========================================================================
    }
  }
</script>
