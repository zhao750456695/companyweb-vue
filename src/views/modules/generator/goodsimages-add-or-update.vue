<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="商品编号" prop="goodsCode">
      <el-input v-model="dataForm.goodsCode" placeholder="商品编号"></el-input>
    </el-form-item>
    <el-form-item label="图片名称" prop="imageName">
      <el-input v-model="dataForm.imageName" placeholder="图片名称"></el-input>
    </el-form-item>
    <el-form-item label="图片链接" prop="imageUrl">
      <el-input v-model="dataForm.imageUrl" placeholder="图片链接"></el-input>
    </el-form-item>
    <el-form-item label="图片种类" prop="imageCategory">
      <el-input v-model="dataForm.imageCategory" placeholder="图片种类"></el-input>
    </el-form-item>
    <el-form-item label="图片在该类中的展示顺序" prop="imageCategorySort">
      <el-input v-model="dataForm.imageCategorySort" placeholder="图片在该类中的展示顺序"></el-input>
    </el-form-item>
    <el-form-item label="上传时间" prop="updated">
      <el-input v-model="dataForm.updated" placeholder="上传时间"></el-input>
    </el-form-item>
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">取消</el-button>
      <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
    </span>
  </el-dialog>
</template>

<script>
  export default {
    data () {
      return {
        visible: false,
        dataForm: {
          goodsCode: '',
          imageId: 0,
          imageName: '',
          imageUrl: '',
          imageCategory: '',
          imageCategorySort: '',
          updated: ''
        },
        dataRule: {
          goodsCode: [
            { required: true, message: '商品编号不能为空', trigger: 'blur' }
          ],
          imageName: [
            { required: true, message: '图片名称不能为空', trigger: 'blur' }
          ],
          imageUrl: [
            { required: true, message: '图片链接不能为空', trigger: 'blur' }
          ],
          imageCategory: [
            { required: true, message: '图片种类不能为空', trigger: 'blur' }
          ],
          imageCategorySort: [
            { required: true, message: '图片在该类中的展示顺序不能为空', trigger: 'blur' }
          ],
          updated: [
            { required: true, message: '上传时间不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.imageId = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.imageId) {
            this.$http({
              url: this.$http.adornUrl(`/generator/goodsimages/info/${this.dataForm.imageId}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.goodsCode = data.goodsimages.goodsCode
                this.dataForm.imageName = data.goodsimages.imageName
                this.dataForm.imageUrl = data.goodsimages.imageUrl
                this.dataForm.imageCategory = data.goodsimages.imageCategory
                this.dataForm.imageCategorySort = data.goodsimages.imageCategorySort
                this.dataForm.updated = data.goodsimages.updated
              }
            })
          }
        })
      },
      // 表单提交
      dataFormSubmit () {
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            this.$http({
              url: this.$http.adornUrl(`/generator/goodsimages/${!this.dataForm.imageId ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'goodsCode': this.dataForm.goodsCode,
                'imageId': this.dataForm.imageId || undefined,
                'imageName': this.dataForm.imageName,
                'imageUrl': this.dataForm.imageUrl,
                'imageCategory': this.dataForm.imageCategory,
                'imageCategorySort': this.dataForm.imageCategorySort,
                'updated': this.dataForm.updated
              })
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
              } else {
                this.$message.error(data.msg)
              }
            })
          }
        })
      }
    }
  }
</script>
