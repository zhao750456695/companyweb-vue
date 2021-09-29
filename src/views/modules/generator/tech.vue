<template>
  <div class="mod-config">
    <div>
        <el-row>
            <h3>制鞋材料详情图设置</h3>
            <el-row style="margin-bottom: 10px">
                <el-table
                :data="dataList1"
                border
                v-loading="dataListLoading"
                style="width: 100%;">
                <el-table-column
                    prop="id"
                    v-if="false"
                    header-align="center"
                    align="center"
                    label="">
                </el-table-column>
                <el-table-column
                    prop="iname"
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
                <el-form :model="dataForm1" :rules="dataRule" ref="dataForm1" @keyup.enter.native="dataFormSubmit1()" label-width="80px">
                <el-form-item label="名称" prop="iname">
                <el-input v-model="dataForm1.iname" placeholder=""></el-input>
                </el-form-item>
                <el-form-item label="展示顺序" prop="iorder">
                  <el-input-number v-model="dataForm1.iorder" :min="1" :max="10" label=""></el-input-number>
                </el-form-item>
                <el-upload
                    ref='bp'
                    action="https://jsonplaceholder.typicode.com/posts/"
                    :http-request="uploadBigPicture"
                    :headers="myHeaders"
                    :limit="1"
                    list-type="picture-card"
                    :on-preview="handlePictureCardPreview"
                    :before-upload="beforeAvatarUpload"
                    :on-remove="handleRemove1">
                    <i class="el-icon-plus"></i>
                </el-upload>
                <el-dialog :visible.sync="dialogVisible">
                    <img width="100%" :src="dialogImageUrl" alt="">
                </el-dialog>
                </el-form>
                <el-row style="margin-top: 10px">
                    <el-button type="success" round @click="dataFormSubmit1()">添加</el-button>
                    <el-button type="warning" round @click="clearall1()">清空</el-button>
                </el-row>
            </el-card>
        </el-row>
    </div>

    <div>
        <el-row>
            <h3>制鞋标准详情图设置</h3>
            <el-row style="margin-bottom: 10px">
                <el-table
                v-on:refreshDataList='getDataList2'
                :data="dataList2"
                border
                style="width: 100%;">
                <el-table-column
                    prop="id"
                    v-if="false"
                    header-align="center"
                    align="center"
                    label="">
                </el-table-column>
                <el-table-column
                    prop="iname"
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
                <el-form :model="dataForm2" :rules="dataRule" ref="dataForm2" @keyup.enter.native="dataFormSubmit2()" label-width="80px">
                <el-form-item label="名称" prop="iname">
                <el-input v-model="dataForm2.iname" placeholder=""></el-input>
                </el-form-item>
                <el-form-item label="展示顺序" prop="iorder">
                  <el-input-number v-model="dataForm2.iorder" :min="1" :max="10" label=""></el-input-number>
                </el-form-item>
                <el-upload
                    ref='bpp'
                    action="https://jsonplaceholder.typicode.com/posts/"
                    :http-request="uploadBigPicture2"
                    :headers="myHeaders"
                    list-type="picture-card"
                    :limit="1"
                    :on-preview="handlePictureCardPreview"
                    :before-upload="beforeAvatarUpload"
                    :on-remove="handleRemove2">
                    <i class="el-icon-plus"></i>
                </el-upload>
                <el-dialog :visible.sync="dialogVisible">
                    <img width="100%" :src="dialogImageUrl" alt="">
                </el-dialog>
                </el-form>
                <el-row style="margin-top: 10px">
                    <el-button type="success" round @click="dataFormSubmit2()">添加</el-button>
                    <el-button type="warning" round @click="clearall2()">清空</el-button>
                </el-row>
            </el-card>
        </el-row>
    </div>

    <div>
        <el-row>
            <h3>检验设备详情图设置</h3>
            <el-row style="margin-bottom: 10px">
                <el-table
                v-on:refreshDataList='getDataList3'
                :data="dataList3"
                border
                style="width: 100%;">
                <el-table-column
                    prop="id"
                    header-align="center"
                    align="center"
                    label="">
                </el-table-column>
                <el-table-column
                    prop="iname"
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
                <el-form :model="dataForm3" :rules="dataRule" ref="dataForm3" @keyup.enter.native="dataFormSubmit3()" label-width="80px">
                <el-form-item label="名称" prop="iname">
                <el-input v-model="dataForm3.iname" placeholder=""></el-input>
                </el-form-item>
                <el-form-item label="展示顺序" prop="iorder">
                  <el-input-number v-model="dataForm3.iorder" :min="1" :max="10" label=""></el-input-number>
                </el-form-item>
                <el-upload
                    ref='bppp'
                    action="https://jsonplaceholder.typicode.com/posts/"
                    :http-request="uploadBigPicture3"
                    :headers="myHeaders"
                    :limit="1"
                    list-type="picture-card"
                    :on-preview="handlePictureCardPreview"
                    :before-upload="beforeAvatarUpload"
                    :on-remove="handleRemove3">
                    <i class="el-icon-plus"></i>
                </el-upload>
                <el-dialog :visible.sync="dialogVisible">
                    <img width="100%" :src="dialogImageUrl" alt="">
                </el-dialog>
                </el-form>
                <el-row style="margin-top: 10px">
                    <el-button type="success" round @click="dataFormSubmit3()">添加</el-button>
                    <el-button type="warning" round @click="clearall3()">清空</el-button>
                </el-row>
            </el-card>
        </el-row>
    </div>
        <!-- <div>
        <el-row>
            <h3>技术规格详情图设置</h3>
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
                    label="">
                </el-table-column>
                <el-table-column
                    prop="bigTitle"
                    header-align="center"
                    align="center"
                    label="大标题">
                </el-table-column>
                <el-table-column
                    prop="smallTitle"
                    header-align="center"
                    align="center"
                    label="小标题">
                </el-table-column>
                <el-table-column
                    prop="link"
                    header-align="center"
                    align="center"
                    label="网页链接">
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
                <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit2()" label-width="80px">
                <el-form-item label="大标题" prop="bigTitle">
                <el-input v-model="dataForm.bigTitle" placeholder=""></el-input>
                </el-form-item>
                <el-form-item label="小标题" prop="smallTitle">
                    <el-input v-model="dataForm.smallTitle" placeholder=""></el-input>
                </el-form-item>
                <el-form-item label="展示顺序" prop="iorder">
                  <el-input-number v-model="dataForm.iorder" :min="1" :max="10" label=""></el-input-number>
                </el-form-item>
                 <el-form-item label="网页链接" prop="link">
                  <el-input v-model="dataForm.link" placeholder=""></el-input>
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
                <el-dialog :visible.sync="dialogVisible">
                    <img width="100%" :src="dialogImageUrl" alt="">
                </el-dialog>
                </el-form>
                <el-row style="margin-top: 10px">
                    <el-button type="success" round @click="dataFormSubmit2()">添加</el-button>
                    <el-button type="warning" round @click="clearall()">清空</el-button>
                </el-row>
            </el-card>
        </el-row>
    </div>

        <div>
        <el-row>
            <h3>检验设备详情图设置</h3>
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
                    label="">
                </el-table-column>
                <el-table-column
                    prop="bigTitle"
                    header-align="center"
                    align="center"
                    label="大标题">
                </el-table-column>
                <el-table-column
                    prop="smallTitle"
                    header-align="center"
                    align="center"
                    label="小标题">
                </el-table-column>
                <el-table-column
                    prop="link"
                    header-align="center"
                    align="center"
                    label="网页链接">
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
                <el-form-item label="大标题" prop="bigTitle">
                <el-input v-model="dataForm.bigTitle" placeholder=""></el-input>
                </el-form-item>
                <el-form-item label="小标题" prop="smallTitle">
                    <el-input v-model="dataForm.smallTitle" placeholder=""></el-input>
                </el-form-item>
                <el-form-item label="展示顺序" prop="iorder">
                  <el-input-number v-model="dataForm.iorder" :min="1" :max="10" label=""></el-input-number>
                </el-form-item>
                 <el-form-item label="网页链接" prop="link">
                  <el-input v-model="dataForm.link" placeholder=""></el-input>
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
    </div> -->

    <add-or-update v-if="addOrUpdateVisible" ref="addOrUpdate" @refreshDataList="getDataList"></add-or-update>
  </div>
</template>

<script>
  import AddOrUpdate from './techimages-add-or-update'
  import Vue from 'vue'
  var token = Vue.cookie.get('token')
  export default {
    data () {
      return {
        num: 1,
        dataForm1: {
          iname: '',
          descri: '',
          iorder: null
        },
        dataForm2: {
          iname: '',
          descri: '',
          iorder: null
        },
        dataForm3: {
          iname: '',
          descri: '',
          iorder: null
        },
        dialogVisible: false,
        dialogImageUrl: '',
        dataList1: [],
        dataList2: [],
        dataList3: [],
        totalPage1: 0,
        pageIndex: 1,
        pageSize: 10,
        totalPage2: 0,
        totalPage3: 0,
        dataListLoading: false,
        dataListSelections: [],
        addOrUpdateVisible: false,
        myHeaders: {token},
        bigPicture: [],
        bigPicture2: [],
        bigPicture3: [],
        dataRule: {
          big: [
            { required: true, message: '大标题不能为空', trigger: 'blur' }
          ],
          small: [
            { required: true, message: '小标题不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    components: {
      AddOrUpdate
    },
    activated () {
      this.getDataList1()
      this.getDataList2()
      this.getDataList3()
    },
    methods: {
      getDataList () {
        this.getDataList1()
        this.getDataList2()
        this.getDataList3()
      },
      getDataList1 () {
        this.dataListLoading = true
        this.$http({
          url: this.$http.adornUrl('/generator/techimages/list'),
          method: 'get',
          params: this.$http.adornParams({
            'page': this.pageIndex,
            'limit': this.pageSize
          })
        }).then(({data}) => {
          if (data && data.code === 0) {
            console.log('data', data.page.list)
            this.dataList1 = []
            for (var index = 0; index < data.page.list.length; index++) {
              if (data.page.list[index].category === 1) {
                this.dataList1.push(data.page.list[index])
              }
            }
            this.totalPage1 = data.page.totalCount
            console.log(this.dataList1)
          } else {
            this.dataList1 = []
            this.totalPage1 = 0
          }
          this.dataListLoading = false
        })
      },
      getDataList2 () {
        this.dataListLoading = true
        this.$http({
          url: this.$http.adornUrl('/generator/techimages/list'),
          method: 'get',
          params: this.$http.adornParams({
            'page': this.pageIndex,
            'limit': this.pageSize
          })
        }).then(({data}) => {
          if (data && data.code === 0) {
            console.log('data', data.page.list)
            this.dataList2 = []
            for (var index = 0; index < data.page.list.length; index++) {
              if (data.page.list[index].category === 2) {
                this.dataList2.push(data.page.list[index])
              }
            }
            this.totalPage2 = data.page.totalCount
            console.log(this.dataList1)
          } else {
            this.dataList2 = []
            this.totalPage2 = 0
          }
          this.dataListLoading = false
        })
      },
      getDataList3 () {
        this.dataListLoading = true
        this.$http({
          url: this.$http.adornUrl('/generator/techimages/list'),
          method: 'get',
          params: this.$http.adornParams({
            'page': this.pageIndex,
            'limit': this.pageSize
          })
        }).then(({data}) => {
          if (data && data.code === 0) {
            console.log('data', data.page.list)
            this.dataList3 = []
            for (var index = 0; index < data.page.list.length; index++) {
              if (data.page.list[index].category === 3) {
                this.dataList3.push(data.page.list[index])
              }
            }
            this.totalPage3 = data.page.totalCount
            console.log(this.dataList1)
          } else {
            this.dataList3 = []
            this.totalPage3 = 0
          }
          this.dataListLoading = false
        })
      },
      dataFormSubmit1 () {
        this.$refs['dataForm1'].validate((valid) => {
          if (valid && this.bigPicture.length > 0) {
            let goodsObj = {
              iname: this.dataForm1.iname,
              descri: this.dataForm1.descri,
              iorder: this.dataForm1.iorder,
              url: this.bigPicture[0],
              category: 1
            }
            console.log(goodsObj)
            this.$http({
              url: this.$http.adornUrl('/generator/techimages/save'),
              method: 'post',
              data: goodsObj
            }).then(({data}) => {
              this.$message({
                message: '添加成功',
                type: 'success'
              })
              this.getDataList1()
              this.clearall1()
              this.$refs.bp.clearFiles()
            })
          }
        })
      },
      clearall1 () {
        this.dataForm1.iname = ''
        this.dataForm1.descri = ''
        this.dataForm1.iorder = null
        this.bigPicture = []
        this.$refs.bp.clearFiles()
      },
      dataFormSubmit2 () {
        this.$refs['dataForm2'].validate((valid) => {
          if (valid && this.bigPicture2.length > 0) {
            let goodsObj = {
              iname: this.dataForm2.iname,
              descri: this.dataForm2.descri,
              iorder: this.dataForm2.iorder,
              url: this.bigPicture2[0],
              category: 2
            }
            console.log(goodsObj)
            this.$http({
              url: this.$http.adornUrl('/generator/techimages/save'),
              method: 'post',
              data: goodsObj
            }).then(({data}) => {
              this.$message({
                message: '添加成功',
                type: 'success'
              })
              this.getDataList2()
              this.clearall2()
              this.$refs.bpp.clearFiles()
            })
          }
        })
      },
      clearall2 () {
        this.dataForm2.iname = ''
        this.dataForm2.descri = ''
        this.dataForm2.iorder = null
        this.bigPicture2 = []
        this.$refs.bpp.clearFiles()
      },
      dataFormSubmit3 () {
        this.$refs['dataForm3'].validate((valid) => {
          if (valid && this.bigPicture3.length > 0) {
            let goodsObj = {
              iname: this.dataForm3.iname,
              descri: this.dataForm3.descri,
              iorder: this.dataForm3.iorder,
              url: this.bigPicture3[0],
              category: 3
            }
            console.log('ggggggg', goodsObj)
            this.$http({
              url: this.$http.adornUrl('/generator/techimages/save'),
              method: 'post',
              data: goodsObj
            }).then(({data}) => {
              this.$message({
                message: '添加成功',
                type: 'success'
              })
              this.getDataList3()
              this.clearall3()
              this.$refs.bppp.clearFiles()
            })
          }
        })
      },
      clearall3 () {
        this.dataForm3.iname = ''
        this.dataForm3.descri = ''
        this.dataForm3.iorder = null
        this.bigPicture3 = []
        this.$refs.bppp.clearFiles()
      },
      uploadBigPicture2 (item) {
        console.log(item.file)
        let fd = new FormData()
        fd.append('file', item.file)
        this.$http({
          url: this.$http.adornUrl('/upload/uploadImage?token=' + this.$cookie.get('token') + '&cat=6'),
          method: 'post',
          data: fd
        }).then(({data}) => {
          item.onSuccess()
          // console.log(data)
          this.bigPicture2.push(data)
        })
      },
      uploadBigPicture3 (item) {
        console.log(item.file)
        let fd = new FormData()
        fd.append('file', item.file)
        this.$http({
          url: this.$http.adornUrl('/upload/uploadImage?token=' + this.$cookie.get('token') + '&cat=6'),
          method: 'post',
          data: fd
        }).then(({data}) => {
          item.onSuccess()
          // console.log(data)
          this.bigPicture3.push(data)
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
            url: this.$http.adornUrl('/generator/techimages/delete'),
            method: 'post',
            data: imgObj
          }).then(({data}) => {
            this.$message({
              message: '删除成功',
              type: 'success'
            })
            this.getDataList1()
            this.getDataList2()
            this.getDataList3()
          })
        }).catch(() => {
          this.$message({
            type: 'info',
            message: '已取消删除'
          })
        })
      },
      handleRemove1 (file, fileList) {
        console.log(file, fileList)
        this.bigPicture = []
      },
      handleRemove2 (file, fileList) {
        console.log(file, fileList)
        this.bigPicture2 = []
      },
      handleRemove3 (file, fileList) {
        console.log(file, fileList)
        this.bigPicture3 = []
        console.log('thisbp3', this.bigPicture3)
      },
      uploadBigPicture (item) {
        console.log(item.file)
        let fd = new FormData()
        fd.append('file', item.file)
        this.$http({
          url: this.$http.adornUrl('/upload/uploadImage?token=' + this.$cookie.get('token') + '&cat=6'),
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
        // this.getDataList1()
        // this.getDataList2()
      },
      saveHeadPicture (index) {
        if (index === 1) {
          var imgObj = {
            url: this.dataForm2.about,
            category: 1
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
        }
        if (index === 2) {
          var imgObj2 = {
            url: this.dataForm2.news,
            category: 2
          }
          this.$http({
            url: this.$http.adornUrl('/generator/sampleimage/save'),
            method: 'post',
            data: imgObj2
          }).then(({data}) => {
            this.$message({
              message: '保存成功',
              type: 'success'
            })
            // this.getDataList1()
          })
        }
        if (index === 3) {
          var imgObj3 = {
            url: this.dataForm2.product,
            category: 3
          }
          this.$http({
            url: this.$http.adornUrl('/generator/sampleimage/save'),
            method: 'post',
            data: imgObj3
          }).then(({data}) => {
            this.$message({
              message: '保存成功',
              type: 'success'
            })
            // this.getDataList1()
          })
        }
        if (index === 4) {
          var imgObj4 = {
            url: this.dataForm2.tech,
            category: 4
          }
          this.$http({
            url: this.$http.adornUrl('/generator/sampleimage/save'),
            method: 'post',
            data: imgObj4
          }).then(({data}) => {
            this.$message({
              message: '保存成功',
              type: 'success'
            })
            // this.getDataList1()
          })
        }
        if (index === 5) {
          var imgObj5 = {
            url: this.dataForm2.contact,
            category: 5
          }
          this.$http({
            url: this.$http.adornUrl('/generator/sampleimage/save'),
            method: 'post',
            data: imgObj5
          }).then(({data}) => {
            this.$message({
              message: '保存成功',
              type: 'success'
            })
            // this.getDataList1()
          })
        }
        if (index === 6) {
          var imgObj6 = {
            url: this.dataForm2.back,
            category: 6
          }
          this.$http({
            url: this.$http.adornUrl('/generator/sampleimage/save'),
            method: 'post',
            data: imgObj6
          }).then(({data}) => {
            this.$message({
              message: '保存成功',
              type: 'success'
            })
            // this.getDataList1()
          })
        }
      }
    }
  }
</script>
