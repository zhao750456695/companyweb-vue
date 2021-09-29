<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :before-close="handleClose"
    :visible.sync="visible">
    <el-form :model="dataForm" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="商品名" prop="goodsName">
      <el-input v-model="dataForm.goodsName" placeholder="商品名"></el-input>
    </el-form-item>
    <el-form-item label="商品编号" prop="goodsCode">
      <el-input @change="goodsCodeChange"  :disabled="codeInputDisable" v-model="dataForm.goodsCode" placeholder="商品编号"></el-input>
    </el-form-item>
    <el-form-item label="商品筛选" prop="goodscategoryId">
      <div v-for="(item, index) in filterList">
        <el-checkbox-group
          v-model="checkedFilters[index]"
          :max="1">
          <el-input
            placeholder="请输入内容"
            v-model="item.label"
            style="width: 80px; margin-right: 26px;"
            :disabled="true">
          </el-input>
          <el-checkbox @change="checkedf()" v-for="(filter, i) in item.children" :label="filter.id" :key="i">{{filter.label}}</el-checkbox>
        </el-checkbox-group>
      </div>
    </el-form-item>

    <el-form-item label="宣传标题" prop="goodsTitle">
      <el-input v-model="dataForm.goodsTitle" placeholder="商品宣传标题"></el-input>
    </el-form-item>

<!--      <el-switch
        v-model="switchValue"
        active-color="#13ce66"
        inactive-color="gray"
        active-text="展示在首页"
        inactive-text="不展示在首页"
        >
      </el-switch> -->

    <el-form-item>
      <div style="text-align:center">
        <h3 style="margin-bottom: 2px;margin-right: 10%">商品参数</h3>
        每个输入框输入一条,输入示例：“尺寸：696x423x565mm”
      </div>

      <div v-for="(item, index) in itemArr" :key="index">
        <el-input v-model="item.content" :placeholder="'参数'+(index+1)"></el-input>
      </div>


      <el-row>
        <el-button type="primary" @click="addInput" style="margin-top: 16px;margin-left: 40%">添加参数</el-button>
      </el-row>
    </el-form-item>

<!--    <el-form-item>
      <div style="text-align:center">
        <h3 style="margin-right: 10%;margin-bottom: 2px">商品亮点</h3>
        <span style="margin-right: 10%">每个输入框输入一条</span>
      </div>

      <div v-for="(item, index) in itemArr1" :key="index">
        <el-input v-model="item.content" :placeholder="'亮点'+(index+1)"></el-input>
      </div>


      <el-row>
        <el-button type="primary" @click="addInput1" style="margin-top: 16px;margin-left: 40%">添加亮点</el-button>
      </el-row>
    </el-form-item> -->

    <div style="text-align:center">
      <h3>商品列表图片</h3>
    </div>
    <el-upload
      action="https://jsonplaceholder.typicode.com/posts/"
      list-type="picture-card"
      :http-request="uploadListPicture"
      :headers="myHeaders"
      :limit="1"
      :file-list="listImage"
      :before-upload="beforeAvatarUpload"
      :on-preview="handlePictureCardPreview1"
      :on-remove="handleRemove1">
      <i class="el-icon-plus"></i>
    </el-upload>
    <el-dialog :visible.sync="dialogVisible1">
      <img width="100%" :src="dialogImageUrl" alt="">
    </el-dialog>

    <div style="text-align:center">
      <h3>商品详情页顶部图片</h3>
    </div>
    <el-upload
      action="https://jsonplaceholder.typicode.com/posts/"
      list-type="picture-card"
      :http-request="uploadHeadPicture"
      :headers="myHeaders"
      :limit="5"
      :file-list="headImages"
      :before-upload="beforeAvatarUpload"
      :on-preview="handlePictureCardPreview2"
      :on-remove="handleRemove2">
      <i class="el-icon-plus"></i>
    </el-upload>
    <el-dialog :visible.sync="dialogVisible2">
      <img width="100%" :src="dialogImageUrl" alt="">
    </el-dialog>

    <div style="text-align:center">
      <h3>商品详情页商品详情大图</h3>
    </div>
    <el-upload
      action="https://jsonplaceholder.typicode.com/posts/"
      :http-request="uploadBigPicture"
      :headers="myHeaders"
      list-type="picture-card"
      :file-list="detailImages"
      :on-preview="handlePictureCardPreview3"
      :before-upload="beforeAvatarUpload"
      :on-remove="handleRemove3">
      <i class="el-icon-plus"></i>
    </el-upload>
    <el-dialog :visible.sync="dialogVisible3">
      <img width="100%" :src="dialogImageUrl" alt="">
    </el-dialog>


    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="cancelClick">取消</el-button>
      <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
    </span>
  </el-dialog>
</template>
<style>
  .v-modal {
  }
  .avatar-uploader .el-upload {
    border: 1px dashed #d9d9d9;
    border-radius: 6px;
    cursor: pointer;
    position: relative;
    overflow: hidden;
  }
  .avatar-uploader .el-upload:hover {
    border-color: #409EFF;
  }
  .avatar-uploader-icon {
    font-size: 28px;
    color: #8c939d;
    width: 178px;
    height: 178px;
    line-height: 178px;
    text-align: center;
  }
  .avatar {
    width: 178px;
    height: 178px;
    display: block;
  }
</style>
<script>
  import Vue from 'vue'
  var token = Vue.cookie.get('token')
  export default {
    data () {
      return {
        checkedFiltersSumbit: [],
        filterList: [],
        checkedFilters: [],
        goodsCodeDuplicated: false,
        codeInputDisable: false,
        selectedOptions: [],
        headImages: [],
        listImage: [],
        detailImages: [],
        switchValue: false,
        props: { multiple: true, expandTrigger: 'hover', value: 'id' },
        value: '',
        visible: false,
        dataForm: {
          goodsId: 0,
          goodsName: '',
          goodsCode: '',
          goodscategoryId: '',
          goodsTitle: '',
          updated: ''
        },
        imageUrl: '',
        dialogImageUrl: '',
        dialogVisible: false,
        dialogVisible1: false,
        dialogVisible2: false,
        dialogVisible3: false,
        fileList: [],
        itemArr: [],
        itemArr1: [],
        myHeaders: {token},
        bigPicture: [],
        headPicture: [],
        listPicture: []
      }
    },
    methods: {
      checkedf () {
        console.log(this.checkedFilters)
        this.getCheckedFiltersLabels()
      },
      handleClose (done) {
        this.$confirm('确认关闭？')
          .then(_ => {
            this.clearAll()
            done()
          })
          .catch(_ => {})
      },
      clearAll () {
        this.bigPicture = []
        this.headPicture = []
        this.listPicture = []
        this.dataForm = {
          goodsId: 0,
          goodsName: '',
          goodsCode: '',
          goodscategoryId: '',
          goodsTitle: '',
          updated: ''
        }
        this.headImages = []
        this.listImage = []
        this.detailImages = []
        this.itemArr = []
        this.itemArr1 = []
        this.selectedOptions = []
        this.codeInputDisable = false
        this.goodsCodeDuplicated = false
      },
      uploadListPicture (item) {
        console.log(item.file)
        let fd = new FormData()
        fd.append('file', item.file)
        this.$http({
          url: this.$http.adornUrl('/upload/uploadImage?token=' + this.$cookie.get('token') + '&cat=0'),
          method: 'post',
          data: fd
        }).then(({data}) => {
          item.onSuccess()
          console.log('hhhhhhhhhh', data)
          this.listImage.push({url: data})
          console.log(this.listImage)
        })
      },
      uploadBigPicture (item) {
        console.log(item.file)
        let fd = new FormData()
        fd.append('file', item.file)
        this.$http({
          url: this.$http.adornUrl('/upload/uploadImage?token=' + this.$cookie.get('token') + '&cat=1'),
          method: 'post',
          data: fd
        }).then(({data}) => {
          item.onSuccess()
          // console.log(data)
          this.detailImages.push({url: data})
          console.log(this.detailImages)
        })
      },
      uploadHeadPicture (item) {
        console.log(item.file)
        let fd = new FormData()
        fd.append('file', item.file)
        this.$http({
          url: this.$http.adornUrl('/upload/uploadImage?token=' + this.$cookie.get('token') + '&cat=2'),
          method: 'post',
          data: fd
        }).then(({data}) => {
          item.onSuccess()
          // console.log(data)
          this.headImages.push({url: data})
          console.log(this.headImages)
        })
      },
      uploaded (res, file, fList) {
        console.log('filelllll')
        console.log(file)
      },
      cancelClick () {
        this.visible = false
        this.clearAll()
      },
      addInput1 () {
        this.itemArr1.push({
          content: ''
        })
      },
      addInput () {
        this.itemArr.push({
          content: ''
        })
      },
      handleRemove1 (file, fileList) {
        console.log(file, fileList)
        this.listImage = fileList
      },
      handleRemove2 (file, fileList) {
        console.log(file, fileList)
        this.headImages = fileList
      },
      handleRemove3 (file, fileList) {
        console.log(file, fileList)
        this.detailImages = fileList
      },
      handlePreview (file) {
        console.log(file)
      },
      handlePictureCardPreview1 (file) {
        this.dialogImageUrl = file.url
        this.dialogVisible1 = true
      },
      handlePictureCardPreview2 (file) {
        this.dialogImageUrl = file.url
        this.dialogVisible2 = true
      },
      handlePictureCardPreview3 (file) {
        this.dialogImageUrl = file.url
        this.dialogVisible3 = true
      },
      handleAvatarSuccess (res, file) {
        this.imageUrl = URL.createObjectURL(file.raw)
      },
      beforeAvatarUpload (file) {
        const isLt2M = file.size / 1024 / 1024 < 20
        if (!isLt2M) {
          this.$message.error('上传图片大小不能超过 20MB!')
        }
        return isLt2M
      },
      init (id, code) {
        // listimage和goods保存在一起，headiamge是1，detailimage是2
        // this.options = this.getCategoryList()
        // console.log(this.options)
        console.log(id, code, '888')
        this.dataForm.goodsId = id || 0
        if (this.dataForm.goodsId !== 0) {
          this.codeInputDisable = true
        }
        this.visible = true
        // this.selectedOptions = [[1, 3], [2, 5]]
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.goodsId) {
            this.$http({
              url: this.$http.adornUrl(`/generator/goods/info/${this.dataForm.goodsId}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                console.log('goodsdata', data.cats)
                this.dataForm.goodsName = data.goods.goodsName
                this.dataForm.goodsCode = data.goods.goodsCode
                this.dataForm.goodsTitle = data.goods.goodsTitle
                this.dataForm.updated = data.goods.updated
                this.getFilterList(data)
                // var arr = data.goods.goodscategoryId.split(',')
                // var dataIntArr = []
                // arr.forEach((data) => {
                //   dataIntArr.push(Number(data))
                // })
                // this.selectedOptions = [dataIntArr]
                // this.selectedOptions = data.cats

                if (data.goods.listImage !== null) {
                  this.listImage.push({url: data.goods.listImage})
                }
                if (data.goods.showInIndex === 1) {
                  this.switchValue = true
                } else {
                  this.switchValue = false
                }
                if (data.goods.params[0] !== '') {
                  data.goods.params.forEach((i) => {
                    this.itemArr.push({content: i})
                  })
                }
                // if (data.goods.brilliant[0] !== '') {
                //   data.goods.brilliant.forEach((i) => {
                //     this.itemArr1.push({content: i})
                //   })
                // }
              }
            })
            this.$http({
              url: this.$http.adornUrl(`/generator/goodsimages/infoList/${code}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                for (var i = 0; i < data.detailImages.length; i++) {
                  this.detailImages.push({url: data.detailImages[i].imageUrl})
                }
                for (i = 0; i < data.headImages.length; i++) {
                  this.headImages.push({url: data.headImages[i].imageUrl})
                }
              }
            })
          }
        })
      },
      getCategoryList () {
        this.dataListLoading = true
        this.$http({
          url: this.$http.adornUrl('/generator/goodscategory/list'),
          method: 'get'
        }).then(({data}) => {
          let array = {}
          let returnArray = []
          data.categoryList.forEach(i => {
            let flag = false
            try {
              data.categoryList.forEach(j => {
                if (j.parentId === i.id) {
                  flag = true
                  throw new Error('haveparent')
                }
              })
            } catch (e) {
              console.log(e)
            }
            if (flag) {
              i['children'] = []
              array[i.id] = i
            }
          })
          data.categoryList.forEach(i => {
            if (i.parentId !== undefined && array[i.parentId] !== undefined && array[i.parentId].children !== undefined) {
              array[i.parentId].children.push(i)
            } else {
              returnArray.push(i)
            }
          })
          console.log(returnArray)
          // this.options = returnArray
        })
      },
      goodsCodeChange () {
        this.$http({
          url: this.$http.adornUrl('/generator/goods/checkGoodsCode'),
          method: 'get',
          params: this.$http.adornParams({
            'goodsCode': this.dataForm.goodsCode
          })
        }).then(({data}) => {
          if (data && data.code === 0) {
            this.$message({
              message: '商品编号重复，无法保存！',
              type: 'fail'
            })
            this.goodsCodeDuplicated = true
          } else {
            this.goodsCodeDuplicated = false
          }
        })
      },
      // 表单提交
      dataFormSubmit () {
        this.$refs['dataForm'].validate((valid) => {
          if (this.goodsCodeDuplicated) {
            this.$message({
              message: '商品编号重复，无法保存！',
              type: 'fail'
            })
            return
          }
          if (this.dataForm.goodsName === null || this.dataForm.goodsName === '' || this.dataForm.goodsCode === '' || this.dataForm.goodsCode === null) {
            this.$message({
              message: '商品名称和编号必须填写，无法保存！',
              type: 'fail'
            })
            return
          }
          if (valid) {
            console.log(this.itemArr)
            let pars = []
            this.itemArr.forEach(i => {
              pars.push(i.content)
            })
            // 亮点
            console.log(this.itemArr1)
            let brill = []
            this.itemArr1.forEach(i => {
              brill.push(i.content)
            })
            // 商品名称
            console.log(this.dataForm.goodsName)
            // 商品编码
            console.log(this.dataForm.goodsCode)
            // 宣传语
            console.log(this.dataForm.goodsTitle)
            // console.log('1', this.headImages)
            // console.log('2', this.detailImages)
            // console.log('3', this.listImage)
            if (this.listImage.length > 0) {
              this.listPicture = [this.listImage[0].url]
            }
            this.bigPicture = []
            this.headPicture = []
            this.headImages.forEach((i) => {
              this.headPicture.push(i.url)
            })
            this.detailImages.forEach((i) => {
              this.bigPicture.push(i.url)
            })
            this.getCheckedFilters()
            let goodsObj = {
              goodsName: this.dataForm.goodsName,
              goodsCode: this.dataForm.goodsCode,
              goodsTitle: this.dataForm.goodsTitle,
              updated: new Date(),
              headImage: this.headPicture,
              detailImage: this.bigPicture,
              listImage: this.listPicture,
              category: this.checkedFiltersSumbit,
              pars: pars,
              brill: brill
            }
            console.log(goodsObj)
            this.$http({
              url: this.$http.adornUrl('/generator/goods/save'),
              method: 'post',
              data: goodsObj
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
                this.clearAll()
              } else {
                this.$message.error(data.msg)
              }
            })
          }
        })
      },
      // 获取筛选数据列表
      getFilterList (data6) {
        this.dataListLoading = true
        this.checkedFilters = []
        this.$http({
          url: this.$http.adornUrl('/generator/filter/list'),
          method: 'get'
        }).then(({data}) => {
          console.log(data)
          let array = {}
          let returnArray = []
          data.filterList.forEach(i => {
            i['children'] = []
            array[i.id] = i
          })
          data.filterList.forEach(i => {
            if (i.parentId !== undefined && array[i.parentId] !== undefined && array[i.parentId].children !== undefined) {
              array[i.parentId].children.push(i)
            } else {
              returnArray.push(i)
            }
          })
          console.log('666666', returnArray)
          returnArray.forEach(i => {
            i.stat = 1
            i.children.forEach(j => {
              j.stat = 1
            })
          })
          this.filterList = returnArray
          this.filterList.forEach(i => {
            this.checkedFilters.push([])
            // this.checkedFiltersCopy.push([])
          })
          console.log(this.checkedFilters)
          this.filterList.forEach((i, index) => {
            i.children.forEach(m => {
              // 每个都和
              data6.cats.forEach(j => {
                if (j.id === m.id) {
                  this.checkedFilters[index].push(m.id)
                }
              })
            })
          })
          console.log('arr', this.checkedFilters)
          console.log('rrrrrrrr', this.filterList)
          console.log('rrrrrrrd', data6.cats)
        })
      },
      getCheckedFilters () {
        this.checkedFiltersSumbit = []
        this.checkedFilters.forEach(i => {
          i.forEach(j => {
            this.checkedFiltersSumbit.push(j)
          })
        })
      },
      // 将筛选转为参数
      getCheckedFiltersLabels () {
        this.addParams = []
        this.itemArr = []
        this.getCheckedFilters()
        this.filterList.forEach(filter => {
          filter.children.forEach(c => {
            // console.log(c, 'cccccccccc')
            this.checkedFiltersSumbit.forEach(data => {
              console.log('data', data)
              if (c.id === data) {
                this.addParams.push({parent: filter.label, child: c.label})
              }
            })
          })
        })
        console.log(this.addParams, 'paramsparams')
        this.addParams.forEach(p => {
          this.itemArr.push({content: p.parent + '：' + p.child})
        })
      }
    }
  }
</script>
