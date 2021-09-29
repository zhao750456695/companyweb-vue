<template>
  <div class="mod-config">
    <div>
        <el-row>
            <h3>首页大图设置</h3>
            <el-row style="margin-bottom: 10px">
                <el-table
                :data="dataList"
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
                    <el-button type="text" size="small" @click="addOrUpdateHandle(scope.row.id)">修改</el-button>
                    <el-button type="text" size="small" @click="deleteHandle(scope.row.id)">删除</el-button>
                    </template>
                </el-table-column>
                </el-table>
            </el-row>
            <el-card class="box-card">
                <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
                <el-form-item label="名称" prop="bigTitle">
                <el-input v-model="dataForm.bigTitle" placeholder=""></el-input>
                </el-form-item>
                <el-form-item label="展示顺序" prop="iorder">
                  <el-input-number v-model="dataForm.iorder" :min="1" :max="10" label=""></el-input-number>
                </el-form-item>
                <el-upload
                    ref='bp'
                    action="https://jsonplaceholder.typicode.com/posts/"
                    :http-request="uploadBigPicture"
                    :headers="myHeaders"
                    list-type="picture-card"
                    :on-preview="handlePictureCardPreview"
                    :before-upload="beforeAvatarUpload"
                    :on-remove="handleRemove3">
                    <i class="el-icon-plus"></i>
                </el-upload>
                <div>注意：图片大小1600*570</div>
                <el-dialog :visible.sync="dialogVisible">
                    <img width="100%" :src="dialogImageUrl" alt="">
                </el-dialog>
                </el-form>
                <el-row style="margin-top: 10px">
                    <el-button type="success" round @click="dataFormSubmit()">添加</el-button>
                    <el-button type="warning" round @click="clearall()">清空</el-button>
                </el-row>
            </el-card>
        </el-row>
    </div>


    <add-or-update v-if="addOrUpdateVisible" ref="addOrUpdate" @refreshDataList="getDataList"></add-or-update>
  </div>
</template>

<script>
  import AddOrUpdate from './webimages-add-or-update'
  import Vue from 'vue'
  // import elImage from 'element-ui'
  var token = Vue.cookie.get('token')
  export default {
    data () {
      return {
        num: 1,
        dataForm: {
          bigTitle: '',
          smallTitle: '',
          iorder: null,
          link: ''
        },
        dialogVisible: false,
        dialogImageUrl: '',
        dataList: [],
        dataList1: [],
        totalPage1: 0,
        pageIndex: 1,
        pageSize: 10,
        totalPage: 0,
        dataListLoading: false,
        dataListSelections: [],
        addOrUpdateVisible: false,
        myHeaders: {token},
        bigPicture: [],
        backPicture: [],
        headPicture: [],
        dataRule: {
          big: [
            { required: true, message: '名称不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    components: {
      AddOrUpdate
    },
    activated () {
      this.getDataList()
    },
    methods: {
      getDataList () {
        this.dataListLoading = true
        this.$http({
          url: this.$http.adornUrl('/generator/webimages/getHomePictureList'),
          method: 'get'
        }).then(({data}) => {
          console.log(data)
          if (data && data.code === 0) {
            this.dataList = data.homeImages
          } else {
            this.pictureList = []
          }
          this.dataListLoading = false
        })
      },
      dataFormSubmit () {
        this.$refs['dataForm'].validate((valid) => {
          if (valid && this.bigPicture.length > 0) {
            let goodsObj = {
              bigTitle: this.dataForm.bigTitle,
              smallTitle: this.dataForm.smallTitle,
              iorder: this.dataForm.iorder,
              link: this.dataForm.link,
              url: this.bigPicture[0],
              category: 1
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
              this.getDataList()
              this.clearall()
              this.$refs.bp.clearFiles()
            })
          }
        })
      },
      clearall () {
        this.dataForm.bigTitle = ''
        this.dataForm.smallTitle = ''
        this.dataForm.iorder = null
        this.dataForm.link = ''
        this.bigPicture = []
        this.$refs.bp.clearFiles()
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
            this.getDataList()
          })
        }).catch(() => {
          this.$message({
            type: 'info',
            message: '已取消删除'
          })
        })
      },
      handleRemove3 (file, fileList) {
        console.log(file, fileList)
        this.bigPicture = fileList
      },
      uploadBigPicture (item) {
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
          this.bigPicture.push(data)
        })
      },
      handlePictureCardPreview (file) {
        this.dialogImageUrl = file.url
        this.dialogVisible = true
      },
      beforeAvatarUpload (file) {
        const isLt2M = file.size / 1024 / 1024 < 20

        if (!isLt2M) {
          this.$message.error('上传图片大小不能超过 20MB!')
        }
        return isLt2M
      },
      addOrUpdateHandle (id) {
        this.addOrUpdateVisible = true
        this.$nextTick(() => {
          this.$refs.addOrUpdate.init(id)
        })
      }
    }
  }
</script>
