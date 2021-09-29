<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :before-close="handleClose"
    :visible.sync="visible">
      <div class="mod-config">
    <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
      <el-form-item>
        <el-button v-if="isAuth('generator:goodscategory:save')" type="primary" @click="addTopFilterHandle()">新增筛选</el-button>
      </el-form-item>
    </el-form>
    <!-- tree -->
    <div class="custom-tree-container">
      <div class="block">
        <el-tree
          :data="data"
          show-checkbox
          node-key="id"
          default-expand-all
          draggable
          :expand-on-click-node="false">
          <span class="custom-tree-node" slot-scope="{ node, data }">
            <span>{{ node.label }}</span>
            <span>
              <el-button
                type="text"
                size="mini"
                @click="() => rename(data)">
                Rename
              </el-button>
              <el-button
                type="text"
                size="mini"
                @click="() => append(data)">
                Append
              </el-button>
              <el-button
                type="text"
                size="mini"
                @click="() => remove(node, data)">
                Delete
              </el-button>
            </span>
          </span>
        </el-tree>
      </div>
    </div>





    <!-- 弹窗, 新增 / 修改 -->
    <rename v-if="addOrUpdateVisible" ref="rename" @refreshDataList="getDataList"></rename>
    <add-top-category v-if="addVisible" ref="addOrUpdate" @refreshDataList="getDataList"></add-top-category>
  </div>
  </el-dialog>
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
    data () {
      const data = [{
        id: 1,
        label: '一级 1',
        children: [{
          id: 4,
          label: '二级 1-1',
          children: [{
            id: 9,
            label: '三级 1-1-1'
          }, {
            id: 10,
            label: '三级 1-1-2'
          }]
        }]
      }, {
        id: 2,
        label: '一级 2',
        children: [{
          id: 5,
          label: '二级 2-1'
        }, {
          id: 6,
          label: '二级 2-2'
        }]
      }, {
        id: 3,
        label: '一级 3',
        children: [{
          id: 7,
          label: '二级 3-1'
        }, {
          id: 8,
          label: '二级 3-2'
        }]
      }]
      return {
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
        data: JSON.parse(JSON.stringify(data)),
        addVisible: false,
        visible: false
      }
    },
    components: {
      Rename,
      AddTopCategory
    },
    activated () {
      this.getDataList()
    },
    methods: {
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
          url: this.$http.adornUrl(`/generator/goodscategory/append`),
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
        const parent = node.parent
        const children = parent.data.children || parent.data
        const index = children.findIndex(d => d.id === data.id)
        children.splice(index, 1)
        this.$http({
          url: this.$http.adornUrl(`/generator/goodscategory/delete?id=` + data.id),
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
          url: this.$http.adornUrl('/generator/filter/list'),
          method: 'get'
        }).then(({data}) => {
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
          console.log(returnArray)
          this.data = returnArray
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
