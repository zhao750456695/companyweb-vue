<template>
  <div class="mod-config">
    <el-row>
        <el-col :span="24"><h3>组织架构</h3></el-col>
    </el-row>
    <el-row>
        <el-input
            type="textarea"
            :rows="2"
            :autosize="{ minRows: 6}"
            placeholder="请输入组织架构简单介绍，详细介绍可以在下面添加"
            v-model="structure">
        </el-input>
    </el-row>
    <el-row>
      <el-upload
          ref='bp'
          action="https://jsonplaceholder.typicode.com/posts/"
          :http-request="uploadPicture"
          :headers="myHeaders"
          :limit=1
          :file-list="structurePicture"
          list-type="picture-card"
          :on-remove="handleRemove">
          <i class="el-icon-plus"></i>
      </el-upload>
      <h4>组织架构图，大小：500*300</h4>
    </el-row>
    <el-row>
        <el-button type="success" round @click="saveStructure">保存组织简介</el-button>
    </el-row>
    <!-- 弹窗, 新增 / 修改 -->
    <!-- <add-or-update v-if="addOrUpdateVisible" ref="addOrUpdate" @refreshDataList="getDataList"></add-or-update> -->
  </div>
</template>

<script>
  import Vue from 'vue'
  var token = Vue.cookie.get('token')
  export default {
    data () {
      return {
        structurePicture: [],
        myHeaders: {token},
        pageIndex: 1,
        pageSize: 10,
        totalPage: 0,
        dataListLoading: false,
        dataListSelections: [],
        addOrUpdateVisible: false,
        structure: ''
      }
    },
    activated () {
      this.getDataList()
    },
    methods: {
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
          this.structurePicture = []
          this.structurePicture.push({url: data})
        })
      },
      // 删除图片
      handleRemove (file, fileList) {
        console.log(file, fileList)
        this.structurePicture = fileList
      },
      getDataList () {
        this.dataListLoading = true
        this.$http({
          url: this.$http.adornUrl('/generator/company/en/list'),
          method: 'get',
          params: this.$http.adornParams({
            'page': this.pageIndex,
            'limit': this.pageSize
          })
        }).then(({data}) => {
          if (data && data.code === 0) {
            this.dataList = data.page.list
            this.totalPage = data.page.totalCount
            console.log(this.dataList)
            // 1简介2详情3历程4详情5架构6详情7资质8详情
            for (var i = 0; i < this.dataList.length; i++) {
              if (this.dataList[i].category === 7) {
                this.structure = JSON.parse(this.dataList[i].content).structure
                this.structurePicture = []
                this.structurePicture.push({url: JSON.parse(this.dataList[i].content).url})
              }
            }
          } else {
            this.dataList = []
            this.totalPage = 0
          }
          this.dataListLoading = false
        })
      },
      saveStructure () {
        var content = {
          'structure': this.structure,
          'url': this.structurePicture[0].url
        }
        content = JSON.stringify(content)
        var data = {
          'content': content,
          'category': 7
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
