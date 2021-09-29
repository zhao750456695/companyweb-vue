<template>
  <div class="mod-config">
      <el-card  v-for="(item, index) in filterBindingData" :key="index" class="box-card"  style="margin-top: 10px">
        <div slot="header" class="clearfix">
          <span>{{item.goodsCategoryEntity.label}}</span>
          <el-button style="float: right; padding: 3px 0" type="text" @click="saveParamGroupBindings(item.goodsCategoryEntity, index)">保存</el-button>
        </div>
        <!-- <div v-for="o in 4" :key="o" class="text item">
          {{'列表内容 ' + o }}
        </div> -->
        <el-checkbox-group v-model="checkList[index]"  @change="handleChange" :max="1">
          <el-checkbox v-for="(item1, index1) in item.paramGroupEntityList" :key="index1" :label="item1.id">{{item1.paramGroupName}}</el-checkbox>
        </el-checkbox-group>
      </el-card>
  </div>
</template>
<style>
    .v-modal {
        z-index: 1888;
    }
</style>
<script>
  import Rename from './filter-rename'
  import AddTopCategory from './filter-update'
  export default {
    created () {
      this.dataListLoading = true
      this.$http({
        url: this.$http.adornUrl('/generator/paramgroup/paramGroupBinding'),
        method: 'get'
      }).then(({data}) => {
        console.log(data)
        this.filterBindingData = data.filterBOList
        console.log('8888888888', this.filterBindingData)
        console.log(this.filterBindingData.length)
        this.filterBindingData.map((item, index) => {
          if (item.checkedId !== null && item.checkedId[0] != null) {
            this.$set(this.checkList, index, item.checkedId)
          } else {
            this.$set(this.checkList, index, [])
          }
        })
        console.log('cl', this.checkList)
      })
    },
    data () {
      return {
        filterBindingData: [],
        checkList: {},
        dataForm: {
          key: ''
        },
        dataList: [],
        pageIndex: 1,
        pageSize: 10,
        totalPage: 0,
        dataListLoading: false,
        dataListSelections: [],
        addOrUpdateVisible: false,
        addVisible: false,
        visible: false
      }
    },
    components: {
      Rename,
      AddTopCategory
    },
    // activated () {
    //   this.dataListLoading = true
    //   this.$http({
    //     url: this.$http.adornUrl('/generator/paramgroup/paramGroupBinding'),
    //     method: 'get'
    //   }).then(({data}) => {
    //     console.log(data)
    //     this.filterBindingData = data.filterBOList
    //     console.log(this.filterBindingData.length)
    //     // for (const index in this.filterBindingData.length) {
    //     //   this.$set(this.checkList, index, [])
    //     // }
    //   })
    // },
    methods: {
      saveParamGroupBindings (goodsCategoryEntity, index) {
        // console.log('123456789')
        // console.log(goodsCategoryEntity)
        // console.log(this.checkList[index])
        this.$http({
          url: this.$http.adornUrl(`/generator/paramgroup/saveParamGroupBindings`),
          method: 'post',
          data: this.$http.adornData({
            'categoryid': goodsCategoryEntity.id,
            'paramgroupid': this.checkList[index][0]
          })
        }).then(({data}) => {
          if (data && data.code === 0) {
            this.$message({
              message: '操作成功',
              type: 'success',
              duration: 1500,
              onClose: () => {
                // this.getDataList()
              }
            })
          } else {
            this.$message.error(data.msg)
          }
        })
      },
      handleChange (v1) {
        console.log(v1)
        console.log(this.checkList)
      },
      handleClose (done) {
        this.$confirm('确认关闭？')
          .then(_ => {
            // this.clearAll()
            done()
          })
          .catch(_ => {})
      },
      init (id, code) {
        this.getDataList()
        this.visible = true
      },
      rename (data) {
        console.log('data', data)
        this.addOrUpdateVisible = true
        this.$nextTick(() => {
          this.$refs.rename.init(data)
        })
      },
      append (data) {
        const newChild = { label: '新建分类', children: [] }
        if (!data.children) {
          this.$set(data, 'children', [])
        }
        data.children.push(newChild)
        this.$http({
          url: this.$http.adornUrl(`/generator/filter/append`),
          method: 'post',
          data: this.$http.adornData({
            'parentId': data.id,
            'label': '新建分类'
          })
        }).then(({data}) => {
          if (data && data.code === 0) {
            this.$message({
              message: '操作成功',
              type: 'success',
              duration: 1500,
              onClose: () => {
                this.getDataList()
              }
            })
            this.addOrUpdateVisible = true
            this.$nextTick(() => {
              this.$refs.rename.init(data.category)
            })
          } else {
            this.$message.error(data.msg)
          }
        })
        // console.log(newChild.id)
      },
      remove (node, data) {
        console.log('node', node)
        const parent = node.parent
        const children = parent.data.children || parent.data
        const index = children.findIndex(d => d.id === data.id)
        children.splice(index, 1)
        this.$http({
          url: this.$http.adornUrl(`/generator/filter/delete?id=` + data.id),
          method: 'get'
        }).then(({data}) => {
          if (data && data.code === 0) {
            this.$message({
              message: '操作成功',
              type: 'success',
              duration: 1500,
              onClose: () => {
                this.getDataList()
              }
            })
          } else {
            this.$message.error(data.msg)
          }
        })
      },
      // 获取数据列表
      getDataList () {
        this.dataListLoading = true
        this.$http({
          url: this.$http.adornUrl('/generator/filter/paramGroupBinding'),
          method: 'get'
        }).then(({data}) => {
          console.log(data)
          this.filterBindingData = data.filterBOList
          // let array = {}
          // let returnArray = []
          // data.filterList.forEach(i => {
          //   i['children'] = []
          //   array[i.id] = i
          // })
          // data.filterList.forEach(i => {
          //   if (i.parentId !== undefined && array[i.parentId] !== undefined && array[i.parentId].children !== undefined) {
          //     array[i.parentId].children.push(i)
          //   } else {
          //     returnArray.push(i)
          //   }
          // })
          // console.log(returnArray)
          // this.data = returnArray
        })
      },
      sonsTree (arr, id) {
        var temp = []
        var lev = 0
        var forFn = function (arr, id, lev) {
          for (var i = 0; i < arr.length; i++) {
            var item = arr[i]
            if (item.parentId === id) {
              item.lev = lev
              temp.push(item)
              forFn(arr, item.catId, lev + 1)
            }
          }
        }
        forFn(arr, id, lev)
        return temp
      },
      // 每页数
      sizeChangeHandle (val) {
        this.pageSize = val
        this.pageIndex = 1
        this.getDataList()
      },
      // 当前页
      currentChangeHandle (val) {
        this.pageIndex = val
        this.getDataList()
      },
      // 多选
      selectionChangeHandle (val) {
        this.dataListSelections = val
      },
      // 新增
      addTopFilterHandle () {
        this.addVisible = true
        this.$nextTick(() => {
          this.$refs.addOrUpdate.init()
        })
      },
      // 删除
      deleteHandle (id) {
        var ids = id ? [id] : this.dataListSelections.map(item => {
          return item.catId
        })
        this.$confirm(`确定对[id=${ids.join(',')}]进行[${id ? '删除' : '批量删除'}]操作?`, '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          this.$http({
            url: this.$http.adornUrl('/generator/goodscategory/delete'),
            method: 'post',
            data: this.$http.adornData(ids, false)
          }).then(({data}) => {
            if (data && data.code === 0) {
              this.$message({
                message: '操作成功',
                type: 'success',
                duration: 1500,
                onClose: () => {
                  this.getDataList()
                }
              })
            } else {
              this.$message.error(data.msg)
            }
          })
        })
      }
    }
  }
</script>
``