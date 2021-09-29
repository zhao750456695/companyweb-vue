<template>
  <div class="mod-config">
    <div>
      <!-- 存储在tb_webimages数据表中 -->
        <el-row>
            <h3>基本信息</h3>
            <el-card class="box-card">
                <el-form :model="dataForm" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
                <el-form-item label="网站名称" prop="webname">
                <el-input v-model="dataForm.webname" placeholder=""></el-input>
                </el-form-item>
                <el-form-item label="宣传语" prop="webslogan">
                <el-input v-model="dataForm.slogan" placeholder=""></el-input>
                </el-form-item>
                <el-form-item label="网站描述" prop="descri">
                <el-input
                type="textarea"
                :autosize="{ minRows: 6, maxRows: 10}"
                v-model="dataForm.descri" placeholder=""></el-input>
                </el-form-item>
                <el-form-item label="关键词" prop="keyword">
                <el-input v-model="dataForm.keyword" placeholder="请使用空格隔离关键词"></el-input>
                </el-form-item>
                <el-form-item label="网站标题" prop="title">
                <el-input v-model="dataForm.title" placeholder=""></el-input>
                </el-form-item>
                <el-form-item label="备案信息" prop="record">
                <el-input v-model="dataForm.record" placeholder=""></el-input>
                </el-form-item>
                <el-form-item label="标题图标" prop="icon">
                <el-upload
                    action="https://jsonplaceholder.typicode.com/posts/"
                    :http-request="uploadBigPicture"
                    :headers="myHeaders"
                    list-type="picture-card"
                    :on-preview="handlePictureCardPreview"
                    :before-upload="beforeAvatarUpload"
                    :file-list="fileList"
                    :limit="1"
                    :on-remove="handleRemove3">
                    <i class="el-icon-plus"></i>
                </el-upload>
                <el-dialog :visible.sync="dialogVisible">
                    <img width="100%" :src="dialogImageUrl" alt="">
                </el-dialog>
                </el-form-item>
                </el-form>
                <el-button type="success" round @click="dataFormSubmit()">保存</el-button>
                <el-button type="warning" round @click="delWebBase()">清空</el-button>
            </el-card>
        </el-row>
    </div>
    <el-row>
      <h3>顶部logo图片</h3>
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
        <div>图片大小：1170*100</div>
        <el-dialog :visible.sync="dialogVisible">
            <img width="100%" :src="dialogImageUrl" alt="">
        </el-dialog>
      </el-card>
    </el-row>
    <el-row>
        <h3>在线商城</h3>
        <el-card class="box-card">
            <el-form :model="mallDataForm" ref="dataForm" @keyup.enter.native="mallDataFormSubmit()" label-width="80px">
            <el-form-item label="商城链接" prop="title">
            <el-input v-model="mallDataForm.link" placeholder=""></el-input>
            </el-form-item>
            <el-form-item label="商城图标" prop="icon">
            <el-upload
                action="https://jsonplaceholder.typicode.com/posts/"
                :http-request="uploadMallPicture"
                :headers="myHeaders"
                list-type="picture-card"
                :on-preview="handlePictureCardPreview"
                :before-upload="beforeAvatarUpload"
                :file-list="mallList"
                :limit="1"
                :on-remove="handleMallRemove">
                <i class="el-icon-plus"></i>
            </el-upload>
            <div>注意：点击下面保存才能生效，链接前面必须有“http://”</div>
            <el-dialog :visible.sync="dialogVisible">
                <img width="100%" :src="dialogImageUrl" alt="">
            </el-dialog>
            </el-form-item>
            </el-form>
            <el-button type="success" round @click="mallDataFormSubmit()">保存</el-button>
            <el-button type="warning" round @click="delMall()">清空</el-button>
        </el-card>
    </el-row>
    <el-row>
        <h3>微信、公众号、小程序</h3>
        <el-card class="box-card">
            <el-form :model="dataForm" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">

            <el-form-item label="二维码" prop="icon">
              <el-upload
                  action="https://jsonplaceholder.typicode.com/posts/"
                  :http-request="uploadQRCode1"
                  :headers="myHeaders"
                  list-type="picture-card"
                  :on-preview="handlePictureCardPreview"
                  :before-upload="beforeAvatarUpload"
                  :file-list="QRCode1"
                  :limit="1"
                  :on-remove="handleQRCode1Remove">
                  <i class="el-icon-plus"></i>
              </el-upload>
              <el-dialog :visible.sync="dialogVisible">
                  <img width="100%" :src="dialogImageUrl" alt="">
              </el-dialog>
            </el-form-item>
            <el-form-item label="二维码" prop="icon">
              <el-upload
                  action="https://jsonplaceholder.typicode.com/posts/"
                  :http-request="uploadQRCode2"
                  :headers="myHeaders"
                  list-type="picture-card"
                  :on-preview="handlePictureCardPreview"
                  :before-upload="beforeAvatarUpload"
                  :file-list="QRCode2"
                  :limit="1"
                  :on-remove="handleQRCode2Remove">
                  <i class="el-icon-plus"></i>
              </el-upload>
            <el-dialog :visible.sync="dialogVisible">
                <img width="100%" :src="dialogImageUrl" alt="">
            </el-dialog>
            </el-form-item>
            <el-form-item label="二维码" prop="icon">
              <el-upload
                  action="https://jsonplaceholder.typicode.com/posts/"
                  :http-request="uploadQRCode3"
                  :headers="myHeaders"
                  list-type="picture-card"
                  :on-preview="handlePictureCardPreview"
                  :before-upload="beforeAvatarUpload"
                  :file-list="QRCode3"
                  :limit="1"
                  :on-remove="handleQRCode3Remove">
                  <i class="el-icon-plus"></i>
              </el-upload>
            <el-dialog :visible.sync="dialogVisible">
                <img width="100%" :src="dialogImageUrl" alt="">
            </el-dialog>
            </el-form-item>
            </el-form>
        </el-card>
    </el-row>
    <el-row>
      <h3>顶部图片</h3>
      注意：图片名称不要有空格，尽量不使用汉字命名。
      <!-- 从20开始，20，21，22，23，24，25，26，27 -->
      <el-card class="box-card">
        <h3>关于春江顶部图片</h3>
         <el-upload
              action="https://jsonplaceholder.typicode.com/posts/"
              :http-request="($event) =>{uploadTopPicture($event,20)}"
              :headers="myHeaders"
              list-type="picture-card"
              :on-preview="handlePictureCardPreview"
              :file-list="topList1"
              :limit="1"
              :on-remove="(...event) =>{handleTopRemove(event,20)}">
              <i class="el-icon-plus"></i>
          </el-upload>
          <el-dialog :visible.sync="dialogVisible">
              <img width="100%" :src="dialogImageUrl" alt="">
          </el-dialog>
        <h3>公司动态顶部图片</h3>
         <el-upload
              action="https://jsonplaceholder.typicode.com/posts/"
              :http-request="($event) =>{uploadTopPicture($event,21)}"
              :headers="myHeaders"
              list-type="picture-card"
              :on-preview="handlePictureCardPreview"
              :file-list="topList2"
              :limit="1"
              :on-remove="(...event) =>{handleTopRemove(event,21)}">
              <i class="el-icon-plus"></i>
          </el-upload>
          <el-dialog :visible.sync="dialogVisible">
              <img width="100%" :src="dialogImageUrl" alt="">
          </el-dialog>
        <h3>展会风采顶部图片</h3>
         <el-upload
              action="https://jsonplaceholder.typicode.com/posts/"
              :http-request="($event) =>{uploadTopPicture($event,22)}"
              :headers="myHeaders"
              list-type="picture-card"
              :on-preview="handlePictureCardPreview"
              :file-list="topList3"
              :limit="1"
              :on-remove="(...event) =>{handleTopRemove(event,22)}">
              <i class="el-icon-plus"></i>
          </el-upload>
          <el-dialog :visible.sync="dialogVisible">
              <img width="100%" :src="dialogImageUrl" alt="">
          </el-dialog>
        <h3>公司新闻顶部图片</h3>
         <el-upload
              action="https://jsonplaceholder.typicode.com/posts/"
              :http-request="($event) =>{uploadTopPicture($event,23)}"
              :headers="myHeaders"
              list-type="picture-card"
              :on-preview="handlePictureCardPreview"
              :file-list="topList4"
              :limit="1"
              :on-remove="(...event) =>{handleTopRemove(event,23)}">
              <i class="el-icon-plus"></i>
          </el-upload>
          <el-dialog :visible.sync="dialogVisible">
              <img width="100%" :src="dialogImageUrl" alt="">
          </el-dialog>
        <h3>产品中心顶部图片</h3>
         <el-upload
              action="https://jsonplaceholder.typicode.com/posts/"
              :http-request="($event) =>{uploadTopPicture($event,24)}"
              :headers="myHeaders"
              list-type="picture-card"
              :on-preview="handlePictureCardPreview"
              :file-list="topList5"
              :limit="1"
              :on-remove="(...event) =>{handleTopRemove(event,24)}">
              <i class="el-icon-plus"></i>
          </el-upload>
          <el-dialog :visible.sync="dialogVisible">
              <img width="100%" :src="dialogImageUrl" alt="">
          </el-dialog>
        <h3>技术中心顶部图片</h3>
         <el-upload
              action="https://jsonplaceholder.typicode.com/posts/"
              :http-request="($event) =>{uploadTopPicture($event,25)}"
              :headers="myHeaders"
              list-type="picture-card"
              :on-preview="handlePictureCardPreview"
              :file-list="topList6"
              :limit="1"
              :on-remove="(...event) =>{handleTopRemove(event,25)}">
              <i class="el-icon-plus"></i>
          </el-upload>
          <el-dialog :visible.sync="dialogVisible">
              <img width="100%" :src="dialogImageUrl" alt="">
          </el-dialog>
        <h3>招贤纳士顶部图片</h3>
         <el-upload
              action="https://jsonplaceholder.typicode.com/posts/"
              :http-request="($event) =>{uploadTopPicture($event,26)}"
              :headers="myHeaders"
              list-type="picture-card"
              :on-preview="handlePictureCardPreview"
              :file-list="topList7"
              :limit="1"
              :on-remove="(...event) =>{handleTopRemove(event,26)}">
              <i class="el-icon-plus"></i>
          </el-upload>
          <el-dialog :visible.sync="dialogVisible">
              <img width="100%" :src="dialogImageUrl" alt="">
          </el-dialog>
        <h3>联系我们顶部图片</h3>
         <el-upload
              action="https://jsonplaceholder.typicode.com/posts/"
              :http-request="($event) =>{uploadTopPicture($event,27)}"
              :headers="myHeaders"
              list-type="picture-card"
              :on-preview="handlePictureCardPreview"
              :file-list="topList8"
              :limit="1"
              :on-remove="(...event) =>{handleTopRemove(event,27)}">
              <i class="el-icon-plus"></i>
          </el-upload>
          <el-dialog :visible.sync="dialogVisible">
              <img width="100%" :src="dialogImageUrl" alt="">
          </el-dialog>
      </el-card>
    </el-row>
  </div>
</template>

<script>
  import Vue from 'vue'
  var token = Vue.cookie.get('token')
  export default {
    data () {
      return {
        topList1: [],
        topList2: [],
        topList3: [],
        topList4: [],
        topList5: [],
        topList6: [],
        topList7: [],
        topList8: [],
        QRCode1: [],
        QRCode2: [],
        QRCode3: [],
        QRCode1url: '',
        QRCode1ur2: '',
        QRCode1ur3: '',
        logoList: [],
        mallList: [],
        mallDataForm: {
          link: ''
        },
        mallurl: '',
        dataForm: {
          webname: '',
          keyword: '',
          title: '',
          descri: '',
          slogan: '',
          record: ''
        },
        myHeaders: {token},
        dataList: [],
        pageIndex: 1,
        pageSize: 10,
        totalPage: 0,
        dataListLoading: false,
        iconurl: '',
        logourl: '',
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
      this.getLogoPicture()
      this.getMallPicture()
      this.getQRCode1()
      this.getQRCode2()
      this.getQRCode3()
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
        this.dataListLoading = true
        this.$http({
          url: this.$http.adornUrl('/generator/webase/list'),
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
              if (this.dataList[i].paramKey === 'WEBASE_KEY') {
                this.configId = this.dataList[i].id
                this.dataForm = JSON.parse(this.dataList[i].paramValue)
                // this.dialogImageUrl = this.dataForm.iconurl
                this.fileList.push({url: this.dataForm.iconurl})
                this.iconurl = this.dataForm.iconurl
              }
            }
          } else {
            this.dataList = []
            this.totalPage = 0
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
          descri: '',
          record: '',
          slogan: ''
        }
        this.fileList = []
        this.configId = 0
        this.iconurl = ''
      },
      dataFormSubmit () {
        if (this.configId === 0) {
          var data = {
            'webname': this.dataForm.webname,
            'descri': this.dataForm.descri,
            'keyword': this.dataForm.keyword,
            'title': this.dataForm.title,
            'iconurl': this.iconurl,
            'slogan': this.dataForm.slogan,
            'record': this.dataForm.record
          }
          this.$http({
            url: this.$http.adornUrl('/generator/webase/save'),
            method: 'post',
            data: data
          }).then(({data}) => {
            this.$message({
              message: '保存成功',
              type: 'success'
            })
          })
        } else {
          data = {
            'webname': this.dataForm.webname,
            'descri': this.dataForm.descri,
            'keyword': this.dataForm.keyword,
            'title': this.dataForm.title,
            'iconurl': this.iconurl,
            'slogan': this.dataForm.slogan,
            'record': this.dataForm.record
          }
          this.$http({
            url: this.$http.adornUrl('/generator/webase/update?id=' + this.configId),
            method: 'post',
            data: data
          }).then(({data}) => {
            this.$message({
              message: '更新成功',
              type: 'success'
            })
          })
        }
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
      uploadTopPicture (item, i) {
        console.log(item, i)
        console.log(item.file)
        let fd = new FormData()
        fd.append('file', item.file)
        this.$http({
          url: this.$http.adornUrl('/upload/uploadImage?token=' + this.$cookie.get('token') + '&cat=3'),
          method: 'post',
          data: fd
        }).then(({data}) => {
          item.onSuccess()
          // this.logourl = data
          // console.log('data', data)
          // this.saveLogoPicture(this.logourl)
          this.saveTopPicture(data, i)
        })
      },
      handleTopRemove (event, i) {
        console.log(event, i)
        // this.logoList = fileList
        // 删除
        this.$http({
          url: this.$http.adornUrl('/generator/sampleimage/deleteByCat'),
          method: 'post',
          data: i
        }).then(({data}) => {
          if (data && data.code === 0) {
            this.$message({
              message: '删除成功',
              type: 'success'
            })
          }
        })
      },
      // 保存logo图片
      saveTopPicture (url, category) {
        var imgObj = {
          url: url,
          category: category
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
      getTopPicture () {
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
            // this.logoList = []
            // console.log('ddd', this.dataList1)
            for (var i = 0; i < data.page.list.length; i++) {
              if (data.page.list[i].category === 20) {
                this.topList1.push({url: data.page.list[i].url})
                // this.logourl = data.page.list[i].url
              }
              if (data.page.list[i].category === 21) {
                this.topList2.push({url: data.page.list[i].url})
                // this.logourl = data.page.list[i].url
              }
              if (data.page.list[i].category === 22) {
                this.topList3.push({url: data.page.list[i].url})
                // this.logourl = data.page.list[i].url
              }
              if (data.page.list[i].category === 23) {
                this.topList4.push({url: data.page.list[i].url})
                // this.logourl = data.page.list[i].url
              }
              if (data.page.list[i].category === 24) {
                this.topList5.push({url: data.page.list[i].url})
              }
              if (data.page.list[i].category === 25) {
                this.topList6.push({url: data.page.list[i].url})
              }
              if (data.page.list[i].category === 26) {
                this.topList7.push({url: data.page.list[i].url})
              }
              if (data.page.list[i].category === 27) {
                this.topList8.push({url: data.page.list[i].url})
              }
            }
          } else {
            this.dataList1 = []
            this.totalPage1 = 0
          }
          this.dataListLoading = false
        })
      },
      // 保存logo图片
      saveLogoPicture (url) {
        var imgObj = {
          url: url,
          category: 8
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
      // 获取logo图片
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
            console.log('ddd', this.dataList1)
            for (var i = 0; i < this.dataList1.length; i++) {
              if (this.dataList1[i].category === 8) {
                this.logoList.push({url: this.dataList1[i].url})
                this.logourl = this.dataList1[i].url
              }
              if (data.page.list[i].category === 20) {
                this.topList1 = []
                this.topList1.push({url: data.page.list[i].url})
                // this.logourl = data.page.list[i].url
              }
              if (data.page.list[i].category === 21) {
                this.topList2 = []
                this.topList2.push({url: data.page.list[i].url})
              }
              if (data.page.list[i].category === 22) {
                this.topList3 = []
                this.topList3.push({url: data.page.list[i].url})
              }
              if (data.page.list[i].category === 23) {
                this.topList4 = []
                this.topList4.push({url: data.page.list[i].url})
              }
              if (data.page.list[i].category === 24) {
                this.topList5 = []
                this.topList5.push({url: data.page.list[i].url})
              }
              if (data.page.list[i].category === 25) {
                this.topList6 = []
                this.topList6.push({url: data.page.list[i].url})
              }
              if (data.page.list[i].category === 26) {
                this.topList7 = []
                this.topList7.push({url: data.page.list[i].url})
              }
              if (data.page.list[i].category === 27) {
                this.topList8 = []
                this.topList8.push({url: data.page.list[i].url})
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
          data: 8
        }).then(({data}) => {
          if (data && data.code === 0) {
            this.$message({
              message: '删除成功',
              type: 'success'
            })
          }
        })
      },
      // =========================================================================
      // 商城图片，保存在webimage表 66
      mallDataFormSubmit () {
        let goodsObj = {
          link: this.mallDataForm.link,
          url: this.mallurl,
          category: 66
        }
        console.log('66', goodsObj)
        this.$http({
          url: this.$http.adornUrl('/generator/webimages/save-and-update'),
          method: 'post',
          data: goodsObj
        }).then(({data}) => {
          this.$message({
            message: '添加成功',
            type: 'success'
          })
        })
      },
      handleMallRemove (file, fileList) {
        console.log(file, fileList)
        this.mallList = fileList
        this.mallurl = ''
        // this.delMall()
      },
      uploadMallPicture (item) {
        console.log(item.file)
        let fd = new FormData()
        fd.append('file', item.file)
        this.$http({
          url: this.$http.adornUrl('/upload/uploadImage?token=' + this.$cookie.get('token') + '&cat=3'),
          method: 'post',
          data: fd
        }).then(({data}) => {
          item.onSuccess()
          this.mallurl = data
          console.log('data', data)
        })
      },
      getMallPicture () {
        this.dataListLoading = true
        this.$http({
          url: this.$http.adornUrl('/generator/webimages/getMallPicture'),
          method: 'get'
        }).then(({data}) => {
          console.log(data)
          if (data && data.code === 0) {
            console.log(data)
            if (data.mallImages[0] !== null && data.mallImages[0] !== undefined) {
              this.mallList = []
              this.mallDataForm.link = data.mallImages[0].link
              this.mallList.push({url: data.mallImages[0].url})
              this.mallurl = data.mallImages[0].url
            }
          } else {
            this.mallList = []
            this.mallDataForm.link = ''
          }
          this.dataListLoading = false
        })
      },
      delMall () {
        this.$http({
          url: this.$http.adornUrl('/generator/webimages/delMallPicture'),
          method: 'get'
        }).then(({data}) => {
          console.log(data)
          if (data && data.code === 0) {
            this.$message({
              message: '清除成功',
              type: 'success'
            })
            this.mallList = []
            this.mallDataForm.link = ''
          }
        })
      },
      // ==================== 微信、小程序、支付宝
      // 上传图片 sampleimage表，category为9
      uploadQRCode1 (item) {
        console.log(item.file)
        let fd = new FormData()
        fd.append('file', item.file)
        this.$http({
          url: this.$http.adornUrl('/upload/uploadImage?token=' + this.$cookie.get('token') + '&cat=3'),
          method: 'post',
          data: fd
        }).then(({data}) => {
          item.onSuccess()
          this.QRCode1url = data
          console.log('data', data)
          this.saveQRCode1(this.QRCode1url)
        })
      },
      // 保存logo图片
      saveQRCode1 (url) {
        var imgObj = {
          url: url,
          category: 9
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
      // 获取logo图片
      getQRCode1 () {
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
            this.QRCode1 = []
            console.log('ddd', data.page.list)
            for (var i = 0; i < data.page.list.length; i++) {
              if (data.page.list[i].category === 9) {
                this.QRCode1.push({url: data.page.list[i].url})
                this.QRCode1url = data.page.list[i].url
              }
            }
          }
          this.dataListLoading = false
        })
      },
      handleQRCode1Remove (file, fileList) {
        console.log(file, fileList)
        this.QRCode1 = fileList
        // 删除
        this.$http({
          url: this.$http.adornUrl('/generator/sampleimage/deleteByCat'),
          method: 'post',
          data: 9
        }).then(({data}) => {
          if (data && data.code === 0) {
            this.$message({
              message: '删除成功',
              type: 'success'
            })
          }
        })
      },
      // ====================
      // 上传图片 sampleimage表，category为10
      uploadQRCode2 (item) {
        console.log(item.file)
        let fd = new FormData()
        fd.append('file', item.file)
        this.$http({
          url: this.$http.adornUrl('/upload/uploadImage?token=' + this.$cookie.get('token') + '&cat=3'),
          method: 'post',
          data: fd
        }).then(({data}) => {
          item.onSuccess()
          this.QRCode2url = data
          console.log('data', data)
          this.saveQRCode2(this.QRCode2url)
        })
      },
      // 保存logo图片
      saveQRCode2 (url) {
        var imgObj = {
          url: url,
          category: 10
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
      // 获取logo图片
      getQRCode2 () {
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
            this.QRCode2 = []
            console.log('ddd', data.page.list)
            for (var i = 0; i < data.page.list.length; i++) {
              if (data.page.list[i].category === 10) {
                this.QRCode2.push({url: data.page.list[i].url})
                this.QRCode2url = data.page.list[i].url
              }
            }
          }
          this.dataListLoading = false
        })
      },
      handleQRCode2Remove (file, fileList) {
        console.log(file, fileList)
        this.QRCode2 = fileList
        // 删除
        this.$http({
          url: this.$http.adornUrl('/generator/sampleimage/deleteByCat'),
          method: 'post',
          data: 10
        }).then(({data}) => {
          if (data && data.code === 0) {
            this.$message({
              message: '删除成功',
              type: 'success'
            })
          }
        })
      },
      // ========================
      // 上传图片 sampleimage表，category为11
      uploadQRCode3 (item) {
        console.log(item.file)
        let fd = new FormData()
        fd.append('file', item.file)
        this.$http({
          url: this.$http.adornUrl('/upload/uploadImage?token=' + this.$cookie.get('token') + '&cat=3'),
          method: 'post',
          data: fd
        }).then(({data}) => {
          item.onSuccess()
          this.QRCode3url = data
          console.log('data', data)
          this.saveQRCode3(this.QRCode3url)
        })
      },
      // 保存logo图片
      saveQRCode3 (url) {
        var imgObj = {
          url: url,
          category: 11
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
      // 获取logo图片
      getQRCode3 () {
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
            this.QRCode3 = []
            console.log('ddd', data.page.list)
            for (var i = 0; i < data.page.list.length; i++) {
              if (data.page.list[i].category === 11) {
                this.QRCode3.push({url: data.page.list[i].url})
                this.QRCode3url = data.page.list[i].url
              }
            }
          }
          this.dataListLoading = false
        })
      },
      handleQRCode3Remove (file, fileList) {
        console.log(file, fileList)
        this.QRCode3 = fileList
        // 删除
        this.$http({
          url: this.$http.adornUrl('/generator/sampleimage/deleteByCat'),
          method: 'post',
          data: 11
        }).then(({data}) => {
          if (data && data.code === 0) {
            this.$message({
              message: '删除成功',
              type: 'success'
            })
          }
        })
      }
    }
  }
  // ===============
</script>
