<template>
  <div>

  <el-carousel v-if="fileList.length" :interval="2000" style="width: 260px; margin: auto;">
    <el-carousel-item v-for="(item, i) in fileList" :key="i" style="width: 260px; height: 200px;">
      <img style="width: 100%;" :src="item.url" />
    </el-carousel-item>
  </el-carousel>

    <el-upload
      class="upload-demo"
      action="https://jsonplaceholder.typicode.com/posts/"
      :http-request="handleUpload"
      :headers="myHeaders"
      :on-remove="handleRemove"
      :file-list="fileList"
      list-type="picture">
      <el-button size="small" type="primary">点击上传</el-button>
      <div slot="tip" class="el-upload__tip">图片尺寸260*200，只能上传jpg/png文件，且不超过1000kb，不超过100张</div>
    </el-upload>
  </div>
</template>

<script>
  import Vue from 'vue'
  var token = Vue.cookie.get('token')
  export default {
    data () {
      return {
        direction: 'rtl',
        myHeaders: {token},
        fileList: []
      }
    },
    activated () {
      this.getFileList()
    },
    methods: {
      getFileList () {
        // 从数据库获取图片
        this.$http({
          url: this.$http.adornUrl('/generator/shows/en/album'),
          method: 'get'
        }).then(data => {
          console.log(data)
          this.fileList = []
          for (var i = 0; i < data.data.album.length; i++) {
            this.fileList.push({url: data.data.album[i].url, id: data.data.album[i].id})
          }
        })
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
          this.fileList.push({url: data})
          console.log(this.fileList)
          // 将图片链接保存到数据库
          // show_id为1，表示历届展会
          var imgObj = {
            url: data,
            showId: 0
          }
          this.$http({
            url: this.$http.adornUrl('/generator/sampleimage/show/en/save'),
            method: 'post',
            data: imgObj
          }).then(data => {
            item.onSuccess()
            this.fileList = []
            this.getFileList()
          })
        })
      },
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
        // 从数据库中删除
        this.$http({
          url: this.$http.adornUrl('/generator/sampleimage/show/en/del'),
          method: 'post',
          data: delId
        }).then(({data}) => {
          this.$message({
            message: '删除成功',
            type: 'success'
          })
          // this.getDataList1()
        })
      }
    }
  }
</script>
<style>
</style>
