<template>
  <div class="mod-config">
    <div>
        <el-row>
            <h3>联系我们</h3>
            <el-card class="box-card">
                <el-form :model="dataForm" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
                <el-form-item label="地址" prop="address">
                <el-input v-model="dataForm.address" placeholder=""></el-input>
                </el-form-item>
                <el-form-item label="电话" prop="phone">
                <el-input v-model="dataForm.phone" placeholder=""></el-input>
                </el-form-item>
                <el-form-item label="传真" prop="fax">
                <el-input v-model="dataForm.fax" placeholder=""></el-input>
                </el-form-item>
                <el-form-item label="邮箱" prop="mail">
                <el-input v-model="dataForm.mail" placeholder=""></el-input>
                </el-form-item>
                <el-form-item label="网站" prop="web">
                <el-input v-model="dataForm.web" placeholder=""></el-input>
                </el-form-item>
                </el-form>
                <el-button type="success" round @click="dataFormSubmit()">保存</el-button>
            </el-card>
        </el-row>
    </div>
    <!-- 弹窗, 新增 / 修改 -->
    <!-- <add-or-update v-if="addOrUpdateVisible" ref="addOrUpdate" @refreshDataList="getDataList"></add-or-update> -->
  </div>
</template>

<script>
  export default {
    data () {
      return {
        dataForm: {
          address: '',
          phone: '',
          fax: '',
          mail: '',
          web: ''
        }
      }
    },
    activated () {
      this.getDataList()
    },
    methods: {
      getDataList () {
        this.dataListLoading = true
        this.$http({
          url: this.$http.adornUrl('/generator/company/list'),
          method: 'get',
          params: this.$http.adornParams({
            'page': this.pageIndex,
            'limit': 100,
            'key': this.dataForm.key
          })
        }).then(({data}) => {
          if (data && data.code === 0) {
            this.dataList = data.page.list
            this.totalPage = data.page.totalCount
            console.log(this.dataList)
            // 改为28
            for (var i = 0; i < this.dataList.length; i++) {
              if (this.dataList[i].category === 28) {
                this.dataForm = JSON.parse(this.dataList[i].content)
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
          'address': this.dataForm.address,
          'phone': this.dataForm.phone,
          'mail': this.dataForm.mail,
          'fax': this.dataForm.fax,
          'web': this.dataForm.web
        }
        content = JSON.stringify(content)
        var data = {
          content: content,
          category: 28
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
