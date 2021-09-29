<template>
  <div class="mod-config">

    <div>
       <el-row>
          <h3>显示一商品</h3>
          <el-card class="box-card">
            <el-input
              placeholder="请输入内容"
              v-model="label1"
              >
            </el-input>
            <div  style="margin-top: 26px;">
              <el-checkbox-group
                v-model="checkedFilters1"
                :max="8">
                <el-checkbox @change="cc()" v-for="(item, index) in productList" :label="item.goodsId" :key="index">{{item.goodsName}}</el-checkbox>
              </el-checkbox-group>
            </div>
            <div style="margin-top: 16px;">
              <el-button type="success" round @click="dataFormSubmit(1)">保存</el-button>
              <el-button type="warning" round @click="delWebBase()">清空</el-button>
            </div>
          </el-card>
        </el-row>


        <el-row>
          <h3>显示二商品</h3>
          <el-card class="box-card">
            <el-input
              placeholder="请输入内容"
              v-model="label2"
              >
            </el-input>
            <div  style="margin-top: 26px;">
              <el-checkbox-group
                v-model="checkedFilters2"
                :max="8">
                <el-checkbox @change="cc()" v-for="(item, index) in productList" :label="item.goodsId" :key="index">{{item.goodsName}}</el-checkbox>
              </el-checkbox-group>
            </div>
            <div style="margin-top: 16px;">
              <el-button type="success" round @click="dataFormSubmit(2)">保存</el-button>
              <el-button type="warning" round @click="delWebBase()">清空</el-button>
            </div>
          </el-card>
        </el-row>

        <el-row>
          <h3>显示三商品</h3>
          <el-card class="box-card">
            <el-input
              placeholder="请输入内容"
              v-model="label3"
              >
            </el-input>
            <div  style="margin-top: 26px;">
              <el-checkbox-group
                v-model="checkedFilters3"
                :max="8">
                <el-checkbox @change="cc()" v-for="(item, index) in productList" :label="item.goodsId" :key="index">{{item.goodsName}}</el-checkbox>
              </el-checkbox-group>
            </div>
            <div style="margin-top: 16px;">
              <el-button type="success" round @click="dataFormSubmit(3)">保存</el-button>
              <el-button type="warning" round @click="delWebBase()">清空</el-button>
            </div>
          </el-card>
        </el-row>

        <el-row>
          <h3>显示四商品</h3>
          <el-card class="box-card">
            <el-input
              placeholder="请输入内容"
              v-model="label4"
              >
            </el-input>
            <div  style="margin-top: 26px;">
              <el-checkbox-group
                v-model="checkedFilters4"
                :max="8">
                <el-checkbox @change="cc()" v-for="(item, index) in productList" :label="item.goodsId" :key="index">{{item.goodsName}}</el-checkbox>
              </el-checkbox-group>
            </div>
            <div style="margin-top: 16px;">
              <el-button type="success" round @click="dataFormSubmit(4)">保存</el-button>
              <el-button type="warning" round @click="delWebBase()">清空</el-button>
            </div>
          </el-card>
        </el-row>

        <el-row>
          <h3>主页产品下方宣传广告图片</h3>
          <el-card class="box-card">
            <el-upload
                action="https://jsonplaceholder.typicode.com/posts/"
                :http-request="uploadPicture"
                :headers="myHeaders"
                list-type="picture-card"
                :on-preview="handlePictureCardPreview"
                :file-list="pictureList"
                :limit="1"
                :on-remove="handlePictureListRemove">
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
        // =========================================
        pictureList: [],
        label1: '',
        label2: '',
        label3: '',
        label4: '',
        myHeaders: {token},
        productList: [],
        checkedFilters1: [],
        checkedFilters2: [],
        checkedFilters3: [],
        checkedFilters4: [],
        // 最终提交的类别的列表
        checkedFiltersSumbit: [],
        checkedFiltersSumbit1: [],
        dialogVisible: false,
        dialogImageUrl: ''
      }
    },
    mounted () {
      this.getProductList()
      this.getPicture()
      // this.getDataList()
    },
    methods: {
      handlePictureListRemove () {
        this.$http({
          url: this.$http.adornUrl('/generator/sampleimage/en/deleteByCat'),
          method: 'post',
          data: 30
        }).then(({data}) => {
          if (data && data.code === 0) {
            this.$message({
              message: '删除成功',
              type: 'success'
            })
          }
        })
      },
      uploadPicture (item) {
        console.log(item.file)
        let fd = new FormData()
        fd.append('file', item.file)
        this.$http({
          url: this.$http.adornUrl('/upload/uploadImage?token=' + this.$cookie.get('token') + '&cat=3'),
          method: 'post',
          data: fd
        }).then(({data}) => {
          item.onSuccess()
          // this.iconurl = data
          console.log('data', data)
          this.savePicture(data)
        })
      },
      savePicture (url) {
        var imgObj = {
          url: url,
          category: 30
        }
        this.$http({
          url: this.$http.adornUrl('/generator/sampleimage/en/save'),
          method: 'post',
          data: imgObj
        }).then(({data}) => {
          this.$message({
            message: '保存成功',
            type: 'success'
          })
        })
      },
      getPicture () {
        this.dataListLoading = true
        this.$http({
          url: this.$http.adornUrl('/generator/sampleimage/en/list'),
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
              if (data.page.list[i].category === 30) {
                this.pictureList.push({url: data.page.list[i].url})
                // this.logourl = data.page.list[i].url
              }
            }
          } else {
            this.dataList1 = []
            this.totalPage1 = 0
          }
          this.dataListLoading = false
        })
      },
      cc () {
        console.log(this.checkedFilters)
      },
      cc1 () {
        console.log(this.checkedFilters1)
      },
      handlePictureCardPreview (file) {
        this.dialogImageUrl = file.url
        this.dialogVisible = true
      },
      // ====================================== 下面设置商品 ==========================
      getData () {
        this.$http({
          url: this.$http.adornUrl('/generator/homeproducts/en/list-all'),
          method: 'get'
        }).then(({data}) => {
          console.log('6666666', data)
          let goods = data.goods
          goods.forEach(g => {
            if (g.category === 1) {
              this.checkedFilters1 = g.products.split(',').map(Number)
              this.label1 = g.label
              console.log('1111111111111', this.checkedFilters1)
            }
            if (g.category === 2) {
              this.label2 = g.label
              this.checkedFilters2 = g.products.split(',').map(Number)
              console.log('222222222222', this.checkedFilters2)
            }
            if (g.category === 3) {
              this.label3 = g.label
              this.checkedFilters3 = g.products.split(',').map(Number)
            }
            if (g.category === 4) {
              this.label4 = g.label
              this.checkedFilters4 = g.products.split(',').map(Number)
            }
          })
        })
      },
      getProductList () {
        this.$http({
          url: this.$http.adornUrl('/generator/goods/en/list-all'),
          method: 'get'
        }).then(({data}) => {
          console.log('9999999999', data)
          this.productList = data.goods
          this.getData()
        })
      },
      // 保存首页商品
      dataFormSubmit (index) {
        let dataObj = {}
        if (index === 1) {
          dataObj.label = this.label1
          dataObj.products = this.checkedFilters1.join(',')
          dataObj.category = 1
        }
        if (index === 2) {
          dataObj.label = this.label2
          dataObj.products = this.checkedFilters2.join(',')
          dataObj.category = 2
        }
        if (index === 3) {
          dataObj.label = this.label3
          dataObj.products = this.checkedFilters3.join(',')
          dataObj.category = 3
        }
        if (index === 4) {
          dataObj.label = this.label4
          dataObj.products = this.checkedFilters4.join(',')
          dataObj.category = 4
        }
        this.$http({
          url: this.$http.adornUrl('/generator/homeproducts/en/save'),
          method: 'post',
          data: dataObj
        }).then(({data}) => {
          this.$message({
            message: '保存成功！',
            type: 'success'
          })
          console.log(data)
        })
      }
    }
  }
</script>
