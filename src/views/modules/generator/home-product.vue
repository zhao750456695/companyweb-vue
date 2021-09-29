<template>
  <div class="mod-config">

    <div>
        <el-row>
          <h3>选择要展示的商品种类</h3>
          <el-card class="box-card">
            <div v-for="(item, index) in filterList" style="margin-top: 26px;">
              <el-checkbox-group
                v-model="checkedFilters[index]"
                :max="1">
                <el-input
                  placeholder="请输入内容"
                  v-model="item.label"
                  style="width: 80px; margin-right: 26px;"
                  :disabled="true">
                </el-input>
                <el-checkbox @change="cc()" v-for="(filter, i) in item.children" :label="filter.id" :key="i">{{filter.label}}</el-checkbox>
              </el-checkbox-group>
            </div>
            <div style="margin-top: 16px;">
              <el-button type="success" round @click="dataFormSubmit()">保存</el-button>
              <el-button type="warning" round @click="delWebBase()">清空</el-button>
            </div>
          </el-card>
        </el-row>
        <el-row>
          <h3>选择要展示的商品</h3>
        <el-card class="box-card">
            <el-button type="success" @click="getGoods()">点我获取商品</el-button>
            <div v-for="(item, index) in productList" style="margin-top: 26px;">
              <el-checkbox-group
                v-model="checkedFilters1[index]"
                :max="6">
                <el-input
                  placeholder="请输入内容"
                  v-model="item.cat"
                  style="width: 80px; margin-right: 26px;"
                  :disabled="true">
                </el-input>
                <el-checkbox @change="cc1()" v-for="(product, i) in item.products" :label="product.id" :key="i">{{product.name}}</el-checkbox>
              </el-checkbox-group>
            </div>
            <div style="margin-top: 16px;">
              <el-button type="success" round @click="dataFormSubmit1()">保存</el-button>
              <el-button type="warning" round @click="delWebBase1()">清空</el-button>
            </div>

          </el-card>
        </el-row>
    </div>
  </div>
</template>

<script>
  import Vue from 'vue'
  var token = Vue.cookie.get('token')
  export default {
    data () {
      return {
        // =========================================
        filterList: [],
        // productList: [{
        //   cat: '安全鞋',
        //   products: [{id: 1, name: 'LC666'}, {id: 2, name: 'LC888'}]
        // },
        // {
        //   cat: '户外鞋',
        //   products: [{id: 3, name: 'cLC666'}, {id: 4, name: 'zLC888'}]
        // }],
        productList: [],
        checkedFilters: [],
        // 最终提交的类别的列表
        checkedFiltersSumbit: [],
        checkedFiltersSumbit1: [],
        myHeaders: {token},
        dataList: [],
        pageIndex: 1,
        pageSize: 10,
        totalPage: 0,
        dataListLoading: false,
        iconurl: '',
        fileList: [],
        configId: 0,
        dialogVisible: false,
        dialogImageUrl: '',
        // ===================================================
        checkedFilters1: []
      }
    },
    mounted () {
      this.getFilterList()
      // this.fileList = []
      // this.getDataList()
    },
    methods: {
      cc () {
        console.log(this.checkedFilters)
      },
      cc1 () {
        console.log(this.checkedFilters1)
      },
      // =============================== 上面分类绑定
      getCheckedFilters () {
        this.checkedFiltersSumbit = []
        this.checkedFilters.forEach(i => {
          i.forEach(j => {
            this.checkedFiltersSumbit.push(j)
          })
        })
      },
      // 获取数据列表
      getFilterList () {
         // 注意：被选中的筛选id是独立保存在 tb_goods_category 表中的
         // 因此，在获取数据时要进行比对，因为tb_filters中的数据可能变化，主要是会
         // 被删除
        this.dataListLoading = true
        this.$http({
          url: this.$http.adornUrl('/generator/filter/list'),
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
          this.filterList.forEach(i => {
            this.checkedFilters.push([])
          })
          console.log('rrrr', returnArray)
          // 从 tb_goods_category 获取被选中的filter
          this.$http({
            url: this.$http.adornUrl('/generator/goodscategory/list'),
            method: 'get'
          }).then(data => {
            console.log('gcdata', data)
            // 每个filterList的children对应一个[[]]中的[]
            this.filterList.forEach((i, index) => {
              i.children.forEach(m => {
                // 每个都和
                data.data.categoryList.forEach(j => {
                  if (j.parentId === m.id) {
                    this.checkedFilters[index].push(m.id)
                  }
                })
              })
            })
           // this.getProductByFliterContent()
          })
        })
      },
      // 保存要在首页显示的类别
      dataFormSubmit () {
        // 传送一个列表到后台
        this.getCheckedFilters()
        // 注意：被选中的筛选id是独立保存在 tb_goods_category 表中的，并且只保存id，label不会保存，
        // 因为取数据时，从tb_filter中取数据
        console.log('dataformsubmit', this.checkedFiltersSumbit)
        this.$http({
          url: this.$http.adornUrl('/generator/goods/save-show-in-index'),
          method: 'post',
          data: this.checkedFiltersSumbit
        }).then(({data}) => {
          this.$message({
            message: '保存成功！',
            type: 'success'
          })
          console.log(data)
        })
      },
      // ====================================== 下面设置商品 ==========================
      getCheckedFilters1 () {
        this.checkedFiltersSumbit1 = []
        this.checkedFilters1.forEach(i => {
          i.forEach(j => {
            this.checkedFiltersSumbit1.push(j)
          })
        })
      },
      // 获取商品
      getProductByFliterContent () {
        this.getCheckedFilters()
        this.checkedFiltersSumbit.map(String)
        this.checkedFilters1 = []
        console.log(this.checkedFilters1, '9998888888888888')
        console.log('2222222222222222222', this.checkedFiltersSumbit)
        this.$http({
          url: this.$http.adornUrl('/generator/goods/show-in-index'),
          method: 'post',
          data: this.checkedFiltersSumbit
        }).then(({data}) => {
          console.log('9999999999', data)
          let goodsList = data.homeGoodsVOList
          this.productList = []
          this.checkedFilters1 = []
          goodsList.forEach((g, i) => {
            this.productList.push({cat: g.filter.label, products: []})
          })
          this.productList.forEach(p => {
            this.checkedFilters1.push([])
          })
          // [{
          //   cat: '安全鞋',
          //   products: [{id: 1, name: 'LC666'}, {id: 2, name: 'LC888'}]
          // },
          goodsList.forEach((g, i) => {
            g.goodsList.forEach((p, j) => {
              this.productList[i].products.push({id: p.goodsId, name: p.goodsName})
            })
          })
          console.log(this.checkedFilters1)
          this.$http({
            url: this.$http.adornUrl('/generator/goodsparams/list-home-goods'),
            method: 'get'
          }).then(data => {
            console.log('gcdata', data)
            // 每个filterList的children对应一个[[]]中的[]
            this.productList.forEach((i, index) => {
              i.products.forEach(m => {
                // 每个都和
                data.data.goodsList.forEach(j => {
                  if (j.parentId === m.id) {
                    this.checkedFilters1[index].push(m.id)
                  }
                })
              })
            })
          })
        })
      },
      // 保存首页商品
      dataFormSubmit1 () {
        // 传送一个列表到后台
        this.getCheckedFilters1()
        // 注意：被选中的筛选id是独立保存在 tb_goods_category 表中的，并且只保存id，label不会保存，
        // 因为取数据时，从tb_filter中取数据
        // 保存在数据表
        console.log(this.checkedFilters1)
        console.log('ttttttttttt', this.checkedFiltersSumbit1)
        this.$http({
          url: this.$http.adornUrl('/generator/goods/update-show-in-index'),
          method: 'post',
          data: this.checkedFiltersSumbit1
        }).then(({data}) => {
          this.$message({
            message: '保存成功！',
            type: 'success'
          })
          console.log(data)
        })
      },
      getGoods () {
        this.getProductByFliterContent()
      }
    }
  }
</script>
