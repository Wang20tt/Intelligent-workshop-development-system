<template>
  <div style="width: 100%;height: 80%;">
    <!--      表格主题       -->
    <el-table
        v-loading="loading"
        id="theMainTable"
        :data="tableData"
        highlight-current-row
        stripe
        style="width: 100%">

      <el-table-column
          prop="verify"
          label="申请类型">
      </el-table-column>

      <el-table-column
          prop="created_time"
          label="申请时间">
      </el-table-column>

      <el-table-column
          prop="created_by"
          label="申请人">
      </el-table-column>
      <el-table-column
          prop="workshop_id"
          label="车间编号">
      </el-table-column>
      <el-table-column
          prop="line_id"
          label="产线编号">
      </el-table-column>
      <el-table-column
          prop="id"
          label="工位编号">
      </el-table-column>
      <el-table-column
          fixed="right"
          label="查看详情"
          width="300">
        <template slot-scope="scope">
          <!--              <el-button style="padding: 8px" type="text" @click="handleClick(row)">查看</el-button>-->
          <router-link :to="{
                path: '/work/apply/info',
                query: {
                  type: scope.row.verify,
                  workshop_id : scope.row.workshop_id,
                  line_id: scope.row.line_id,
                  id: scope.row.id
                }
              }">
            <el-button style="padding: 8px" type="text">查看</el-button>
          </router-link>
        </template>
      </el-table-column>
    </el-table>
    <!--      下面是分页效果  -->
    <div class="block" style="padding: 10px">
      <el-pagination

          @prev-click="preclick"
          @next-click="nextclick"
          @current-change="curChange"
          :hide-on-single-page=true
          :total="total"
          background
          layout="total,prev, pager, next, jumper"
          :page-size="page.pages">
      </el-pagination>
    </div>
  </div>
</template>

<script>
import {request} from "@/network/request";

export default {
  name: "applyLine",
  data(){
    return {
      loading:false,
      page:{
        pages: 15,
        current: 1,
      },
      // sortForm: '车间编号',
      tableData:[],
      total:1,



    }
  },
  created() {
    this.getData()
  },
  activated() {
    this.$router.push(this.path)
  },
  beforeRouteLeave(to, from, next){
    this.path = this.$route.path;
    next();
  },
  methods:{
    getData(){
      console.log(this.page.current)
      let req= {
        current: this.page.current,  //页数 从1开始
        pages: this.page.pages  // 每页多少数据
      }

      request({
        url:'station/applyStation',
        method:'get',
        params: req
      }).then(res=>{
        console.log('station ：',res)
        this.total = res.data.result.total
        this.tableData = res.data.result.records
        //   [
        //     total: '',总条数
        //       [
        //         {
        //            workshop_id：车间id
        //            created_by：申请人
        //            type：申请类型
        //            created_time ：申请时间
        //          }
        //      ]
        // ]
        //   this.tableData = res.tableData
        //   this.total = res.total
      }).catch(err=>{

      })



    },


    preclick(current){
      this.page.current=current
      this.loading = true
      setTimeout(()=>{
        this.getData()
        this.loading=false
      },1000)
    },
    nextclick(current){
      this.page.current=current
      this.loading = true
      setTimeout(()=>{
        this.getData()
        this.loading=false
      },1000)

    },
    curChange(current){
      this.page.current=current
      this.loading = true
      setTimeout(()=>{
        this.getData()
        this.loading=false
      },1000)
    },
  },
}
</script>

<style scoped>

</style>