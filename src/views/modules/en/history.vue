<template>
  <div class="mod-config">
    <div>
        <el-row>
            <el-col :span="24"><h3>发展历程</h3></el-col>
        </el-row>
        <el-row>
            <el-input
                type="textarea"
                :rows="2"
                :autosize="{ minRows: 6}"
                placeholder="请输入发展历程简单介绍"
                v-model="history">
            </el-input>
        </el-row>
        <el-row>
            <el-button type="success" round @click="saveHistory">保存历程简介</el-button>
        </el-row>
        <el-row>
            <el-col :span="24"><h3>时间轴</h3></el-col>
        </el-row>
        <el-row v-for="(timeline, ri) in timeList" :key="ri" style="margin-top: 36px;">
          <el-card class="box-card" style="width: 60%;margin: auto;">
            <div slot="header" class="clearfix">
               <div slot="header" class="clearfix">
                 <el-input
                   placeholder="请输入年份"
                   suffix-icon="el-icon-edit"
                   v-model="timeline.tyear"
                   style="width: 80%;">
                 </el-input>
                 <el-button v-if="ri===(timeList.length-1)" style="float: right; padding: 3px 0; margin-left: 10px;" type="danger" @click="delTimeline(ri)">删除</el-button>
                 <el-button style="float: right; padding: 3px 0" type="success" @click="saveTimeline(timeline, ri)">保存</el-button>
               </div>
            </div>

              <el-input v-model="timeline.title" placeholder="请输入大标题">
              </el-input>
              <el-input type="textarea" :rows="6" v-model="timeline.content" placeholder="请输入内容">
              </el-input>

          </el-card>
        </el-row>

        <el-button type="primary" @click="addCard()" style="margin: auto; display: block; margin-top: 36px;">延长时间轴</el-button>

    </div>
    <!-- <add-or-update v-if="addOrUpdateVisible" ref="addOrUpdate" @refreshDataList="getDataList"></add-or-update> -->
  </div>
</template>

<script>
  // import Vue from 'vue'
  // var token = Vue.cookie.get('token')
  export default {
    data () {
      return {
        // timeList: [{year: '2001', title: '公司成立', content: '公司成立666'}, {year: '2002', title: '公司飞速发展', content: '公司飞速发展666'}],
        timeList: [],
        timelineTitle: '',
        timelineContent: '',
        pageIndex: 1,
        pageSize: 10,
        totalPage: 0,
        dataListLoading: false,
        dataListSelections: [],
        addOrUpdateVisible: false,
        history: '',
        isHistory: false,
        historyId: 0
      }
    },
    activated () {
      this.getDataList()
      this.getTimeLine()
    },
    methods: {
      // 删除
      // 根据队列的实际index+1确定torder
      // 只能删除最后一个，其他的只是修改
      delTimeline (ri) {
        // let stat = this.timeList[ri].stat
        // let id = this.timeList[ri].id
        // 删除ui中的card
        this.timeList.splice(ri, 1)
        let torder = this.timeList.length + 1
        // 根据torder进行删除，数据库存在就删除，不存在就不操作
        this.$http({
          url: this.$http.adornUrl('/generator/timeline/en/del'),
          method: 'post',
          data: torder
        }).then(({data}) => {
          console.log(data)
          this.$message({
            message: '删除成功',
            type: 'success'
          })
          // this.getDataList()
        })
        // 如果stat为1删除数据库中的数据
        // if (stat === 1) {
        //   this.$http({
        //     url: this.$http.adornUrl('/generator/timeline/del'),
        //     method: 'post',
        //     data: id
        //   }).then(({data}) => {
        //     console.log(data)
        //     this.$message({
        //       message: '删除成功',
        //       type: 'success'
        //     })
        //     // this.getDataList()
        //   })
        // }
      },
      getTimeLine () {
        this.$http({
          url: this.$http.adornUrl('/generator/timeline/en/list'),
          method: 'get'
        }).then(({data}) => {
          console.log(data)
          this.$message({
            message: '获取成功',
            type: 'success'
          })
          this.timeList = []
          data.timelines.forEach(t => {
            this.timeList.push(t)
          })
        })
      },
      // 保存一个筛选
      saveTimeline (timeline, ri) {
        console.log('llllll', timeline)
        // timeline.torder = ri
        this.$http({
          url: this.$http.adornUrl('/generator/timeline/en/save'),
          method: 'post',
          data: timeline
        }).then(({data}) => {
          console.log(data)
          this.$message({
            message: '添加成功',
            type: 'success'
          })
          // this.getDataList()
        })
      },
      addCard () {
        var ind = this.timeList.length
        console.log(ind)
        console.log('88888888888', this.timeList[1])
        var obj = this.timeList[ind - 1]
        console.log(obj)
        this.timeList.push({
          year: '',
          title: '',
          content: '',
          torder: this.timeList.length + 1
        })
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
              if (this.dataList[i].category === 6) {
                this.history = this.dataList[i].content
              }
            }
          } else {
            this.totalPage = 0
          }
          this.dataListLoading = false
        })
      },
      saveHistory () {
        var data = {
          'content': this.history,
          'category': 6
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
