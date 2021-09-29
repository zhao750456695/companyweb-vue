<template>
   <div class="mod-config">
        <el-row>
            <el-collapse>
                <el-collapse-item title="查看说明" name="1">
                    <img src="http://weidu-company.oss-cn-beijing.aliyuncs.com/20210908/9773ebbcfec54a4186155b88ef4a86ce.png"/>
                </el-collapse-item>
            </el-collapse>
        </el-row>
  <el-alert
    title="注意:无论是删除还是新增,都需要点击保存按钮才能生效"
    type="warning">
  </el-alert>
    <el-row v-for="(filter, ri) in filterList" :key="ri" style="margin-top: 36px;">
      <el-card class="box-card" style="width: 60%;margin: auto;">
        <div slot="header" class="clearfix">
           <div slot="header" class="clearfix">
             <el-input
               placeholder="请输入筛选名称"
               suffix-icon="el-icon-edit"
               v-model="filter.label"
               style="width: 80%;">
             </el-input>
             <el-button style="float: right; padding: 3px 0; margin-left: 10px;" type="danger" @click="delFilter(ri)">删除</el-button>
             <el-button style="float: right; padding: 3px 0" type="success" @click="saveFilter(filter)">保存</el-button>
           </div>
        </div>

        <div v-for="(item, ii) in filter.children" :key="ii" class="text item">
          <el-input v-model="item.label" placeholder="请输入筛选内容">
            <el-button style="padding-right:10px" slot="suffix" type="text" @click="delItem(ri, ii)">删除</el-button>
          </el-input>
        </div>

        <el-button type="warning" round style="margin-top: 26px;" @click="addItem(ri)">新增一条</el-button>

      </el-card>
    </el-row>

    <el-button type="primary" @click="addCard()" style="margin: auto; display: block; margin-top: 36px;">新增筛选</el-button>

  </div>
</template>
<style>
</style>
<script>
  export default {
    data () {
      return {
        filterList: [],
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
    activated () {
      this.getDataList()
    },
    methods: {
      // 增加一张卡
      addCard () {
        var ind = this.filterList.length
        console.log(ind)
        console.log('88888888888', this.filterList[1])
        var obj = this.filterList[ind - 1]
        console.log(obj)
        this.filterList.push({
          id: obj.id + 1,
          stat: 0,
          children: []
        })
      },
      // 增加一个条目
      addItem (ri) {
        this.filterList[ri].children.push({label: '', stat: 0})
      },
      delItem (ri, ii) {
        this.filterList[ri].children.splice(ii, 1)
      },
      // 删除一个筛选
      delFilter (ri) {
        let stat = this.filterList[ri].stat
        let id = this.filterList[ri].id
        // 删除ui中的card
        this.filterList.splice(ri, 1)
        // 如果stat为1删除数据库中的数据
        if (stat === 1) {
          this.$http({
            url: this.$http.adornUrl('/generator/filter/en/delFilter'),
            method: 'post',
            data: id
          }).then(({data}) => {
            console.log(data)
            this.$message({
              message: '删除成功',
              type: 'success'
            })
            this.getDataList()
          })
        }
      },
      // 保存一个筛选
      saveFilter (filter) {
        console.log('llllll', filter)
        this.$http({
          url: this.$http.adornUrl('/generator/filter/en/saveFilter'),
          method: 'post',
          data: filter
        }).then(({data}) => {
          console.log(data)
          this.$message({
            message: '添加成功',
            type: 'success'
          })
          this.getDataList()
        })
      },
      remove (node, data) {
        console.log('node', node)
        const parent = node.parent
        const children = parent.data.children || parent.data
        const index = children.findIndex(d => d.id === data.id)
        children.splice(index, 1)
        this.$http({
          url: this.$http.adornUrl(`/generator/filter/en/delete?id=` + data.id),
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
          url: this.$http.adornUrl('/generator/filter/en/list'),
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
        })
      }
    }
  }
</script>
