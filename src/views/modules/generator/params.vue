<template>
   <div class="mod-config">
    
    <el-button type="primary" @click="addInput" style="margin-top: 16px;margin-left: 40%">添加参数组</el-button>

    <el-card  v-for="(item, index) in paramGroups" :key="index" class="box-card"  style="margin-top: 10px">
        <div slot="header" class="clearfix">
            <span> <el-input v-model="item.paramGroupName" placeholder="请输入参数组名称" style="width:60%"></el-input></span>
            <el-button style="float: right; padding: 3px 5px" type="text" @click="savePGName(item.id, item.paramGroupName)">修改名称</el-button>
            <el-button style="float: right; padding: 3px 5px; margin-right: 16px" type="text" @click="delPG(item.id)">删除参数组</el-button>
        </div>

    <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
      <el-form-item>
        <el-button v-if="isAuth('generator:goodscategory:save')" type="primary" @click="addTopParamsHandle(item.id)">新增参数分类</el-button>
      </el-form-item>
    </el-form>
    <!-- tree -->
    <div class="custom-tree-container">
      <div class="block">
        <el-tree
          :data="item.paramsList"
          show-checkbox
          node-key="id"
          default-expand-all
          draggable
          ref="tree"
          :expand-on-click-node="false">
          <span class="custom-tree-node" slot-scope="{ node, data }">
            <span>{{ node.label }}</span>
            <span>
              <el-button
                type="text"
                size="mini"
                @click="() => rename(data, item.id)">
                Rename
              </el-button>
              <el-button
                v-if="data.parentId===0"
                type="text"
                size="mini"
                @click="() => append(data, item.id)">
                Append
              </el-button>
              <el-button
                type="text"
                size="mini"
                @click="() => remove(node, data)">
                Delete
              </el-button>
              <el-button
                v-if="data.parentId!==0"
                type="text"
                size="mini"
                @click="() => showInGoods(node, data, index)">
                参数在商品列表中展示
              </el-button>
            </span>
          </span>
        </el-tree>
      </div>
    </div>      
    </el-card>







    <!-- 弹窗, 新增 / 修改 -->
    <rename v-if="addOrUpdateVisible" ref="rename" @refreshDataList="getDataList"></rename>
    <add-top-category v-if="addVisible" ref="addOrUpdate" @refreshDataList="getDataList"></add-top-category>
    <paramgroup-add v-if="addPGVisible" ref="paramgroupAdd" @refreshDataList="getDataList"></paramgroup-add>

  </div>
</template>
<style>
    .v-modal {
        z-index: 1888;
    }
</style>
<script>
  import Rename from './params-rename'
  import AddTopCategory from './params-update'
  import ParamgroupAdd from './paramgroup-add'

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
        paramGroups: [],
        dataList: [],
        pageIndex: 1,
        pageSize: 10,
        totalPage: 0,
        dataListLoading: false,
        dataListSelections: [],
        addOrUpdateVisible: false,
        data: JSON.parse(JSON.stringify(data)),
        addVisible: false,
        visible: false,
        addPGVisible: false
      }
    },
    components: {
      Rename,
      AddTopCategory,
      ParamgroupAdd
    },
    activated () {
      this.getDataList()
    },
    methods: {
      savePGName (id, pgName) {
        this.$http({
          url: this.$http.adornUrl(`/generator/paramgroup/update`),
          method: 'post',
          data: this.$http.adornData({
            'paramGroupName': pgName,
            'id': id
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
          } else {
            this.$message.error(data.msg)
          }
        })
      },
      delPG (id) {
        console.log('delid', id)
        this.$http({
          url: this.$http.adornUrl(`/generator/paramgroup/delete?id=` + id),
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
      addInput () {
        // this.paramGroups.push([])
        this.addPGVisible = true
        this.$nextTick(() => {
          console.log(this.$refs)
          this.$refs.paramgroupAdd.init()
        })
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
      rename (data, id) {
        console.log('data', data)
        this.addOrUpdateVisible = true
        this.$nextTick(() => {
          this.$refs.rename.init(data, id)
        })
      },
      showInGoods (node, data, index) {
        //
        console.log('ddddd', data)
        let checkedArray = this.$refs.tree[index].getCheckedKeys()
        if (checkedArray.includes(data.id) && checkedArray.includes(data.parentId)) {
          console.log('dataid', data.id)
          let cindex = checkedArray.indexOf(data.id)
          let pindex = checkedArray.indexOf(data.parentId)
          let r1 = checkedArray.splice(cindex, 1)
          checkedArray.splice(pindex, 1)
          this.updateShowInGoods(r1[0], false)
          // this.updateShowInGoods(r2[0], false)
        } else if (checkedArray.includes(data.id)) {
          let cindex = checkedArray.indexOf(data.id)
          let r3 = checkedArray.splice(cindex, 1)
          this.updateShowInGoods(r3[0], false)
        } else {
          console.log('dataid2', data.id)
          checkedArray.push(data.id)
          this.updateShowInGoods(data.id, true)
        }
        this.$refs.tree[index].setCheckedKeys(checkedArray)
        console.log('cccnnn', checkedArray)
      },
      updateShowInGoods (id, isShow) {
        // 传递到后台保存
        this.$http({
          url: this.$http.adornUrl(`/generator/goodsparams/update`),
          method: 'post',
          data: this.$http.adornData({
            'id': id,
            'showInGoods': isShow
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
      append (data, id) {
        const newChild = { label: '新建分类', children: [] }
        if (!data.children) {
          this.$set(data, 'children', [])
        }
        data.children.push(newChild)
        this.$http({
          url: this.$http.adornUrl(`/generator/goodsparams/append`),
          method: 'post',
          data: this.$http.adornData({
            'parentId': data.id,
            'label': '新建分类',
            'paramGroupId': id
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
          url: this.$http.adornUrl(`/generator/goodsparams/delete?id=` + data.id),
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
          url: this.$http.adornUrl('/generator/goodsparams/list'),
          method: 'get'
        }).then(({data}) => {
          this.paramGroups = data.paramGroups
          console.log(data)

          this.paramGroups.forEach((j, index) => {
            let array = {}
            let returnArray = []
            // let checked = []
            j.paramsList.forEach(i => {
              i['children'] = []
              array[i.id] = i
              i['disabled'] = true
            //   if (i.showInGoods !== null && i.showInGoods === true) {
            //     checked.push(i)
            //   }
            })
            j.paramsList.forEach(i => {
              if (i.parentId !== undefined && array[i.parentId] !== undefined && array[i.parentId].children !== undefined) {
                array[i.parentId].children.push(i)
              } else {
                returnArray.push(i)
              }
            })
            console.log(returnArray)
            j.paramsList = returnArray
            let checked = []
            j.paramsList.forEach(i => {
              console.log('jc', i)
              i.children.forEach(m => {
                if (m.showInGoods !== null && m.showInGoods === true) {
                  checked.push(m.id)
                }
                console.log('mm', m)
              })
            })
            console.log('checked', checked)
            console.log('index', index)
            if (checked.length > 0) {
              // this.$refs.tree[index].setCheckedKeys(checked)
            }
            this.$nextTick(() => {
              this.$refs.tree[index].setCheckedKeys(checked)
            })
            // this.$refs.tree[index].setCheckedKeys(checked)
          })
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
      // 新增
      addTopParamsHandle (id) {
        this.addVisible = true
        // console.log(this.paramGroups)
        this.$nextTick(() => {
          this.$refs.addOrUpdate.init(id)
        })
      }
    }
  }
</script>
