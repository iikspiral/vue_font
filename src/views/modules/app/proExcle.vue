<template>
  <div class="hello">
    <el-input placeholder="请输入内容" class="header-input" v-model="lookup"></el-input>
    <el-button type="primary" icon="el-icon-search" style="margin-left:10px;" @click="lookupClick">搜索</el-button>
    <div style="clear:both;"></div>
    <el-table
      :data="tableData"
      :row-class-name="rowClassName"
      @row-click="details"
      v-loading="dataListLoading"
      border
      class="tables"
      style="width: 100%" 
      >
      <el-table-column prop="month" align="center" label="月份"></el-table-column>
      <el-table-column prop="idCard" align="center" label="工号"></el-table-column>
      <el-table-column prop="engAward" align="center" label="工程奖金"></el-table-column>
      <el-table-column prop="magAward" align="center" label="管理奖金"></el-table-column>
      <el-table-column prop="payTotalBook" align="center" label="应发合计"></el-table-column>
      <el-table-column prop="cutTotal" align="center" label="扣款合计"></el-table-column>
      <el-table-column prop="combSalary" align="center" label="实发合计"></el-table-column>
      <el-table-column align="center" label="查看">
        <template slot-scope="scope">
          <el-button type="text" size="small" @click="detail">详情</el-button>
        </template>
      </el-table-column>
    </el-table>
    <!-- 分页 -->
    <el-pagination 
      background
      small
      layout="prev, pager, next, jumper"
      :total="total"
      @current-change="current_change">
    </el-pagination>
    <!-- 详情 -->
    <el-dialog
      title="详情"
      :visible.sync="dialogVisibles"
      width="90%">
      <el-table
        :data="tableDatas"
        border
        class="tables"
        style="width: 100%">
        <el-table-column prop="basePay" align="center" label="基本工资" ></el-table-column>
        <el-table-column prop="postWage" align="center" label="岗位工资"></el-table-column>
        <el-table-column prop="bonusPay" align="center" label="年功工资"></el-table-column>
        <el-table-column prop="overTimePay" align="center" label="加班费"></el-table-column>
        <el-table-column prop="subsidySingleSon" align="center" label="独子补助"></el-table-column>
        <el-table-column prop="allowanceHeat" align="center" label="取暖补助"></el-table-column>
        <el-table-column prop="subsidyRep" align="center" label="补发工资"></el-table-column>
        <el-table-column prop="other" align="center" label="其他"></el-table-column>
        <el-table-column prop="endInsura" align="center" label="养老保险"></el-table-column>
        <el-table-column prop="medInsura" align="center" label="医疗保险"></el-table-column>
        <el-table-column prop="unemployInsura" align="center" label="失业保险"></el-table-column>
        <el-table-column prop="proReserve1" align="center" label="公积金个人" width="100"></el-table-column>
        <el-table-column prop="annuity" align="center" label="年金"></el-table-column>
        <el-table-column prop="taxAgencyAccount" align="center" label="代扣税"></el-table-column>
        <el-table-column prop="cutSalary" align="center" label="工资扣费"></el-table-column>
        <el-table-column prop="dues" align="center" label="会费"></el-table-column>
        <el-table-column prop="rent" align="center" label="房费"></el-table-column>
        <el-table-column prop="waterBill" align="center" label="水费"></el-table-column>
        <el-table-column prop="heatingFee" align="center" label="取暖费"></el-table-column>
        <el-table-column prop="insureSingleSon" align="center" label="独子保险"></el-table-column>
        <el-table-column prop="insureChange" align="center" label="保险补扣"></el-table-column>
        <el-table-column prop="fealtyMoney" align="center" label="孝敬金"></el-table-column>
        <el-table-column prop="cutOther" align="center" label="其他扣款"></el-table-column>
      </el-table>
    </el-dialog>
  </div>
</template>
<script>
  export default {
    name: 'proExcle',
    data () {
      return {
      lookup: '',
      dataListLoading: false,
      dialogVisibles: false,
      tableData: [],
      tableDatas: [],
      fileList: [],
      total:0,
      pagesize:10,
      currentPage:1
      }
    },
    created(){
      this.getDataList();
    },
    methods: {
      // 为每一行添加index
      rowClassName ({row, rowIndex}) {
        row.index = rowIndex;
      },
      // 详情
      details (row) {
        if (!this.dialogVisibles) {// 如果详情页没有出现，就不执行下面的数据赋值
          return;
        }
        this.tableDatas = [];
        this.tableDatas.push(this.tableData[row.index]);
      },
      detail () {
        this.dialogVisibles = true;
      },
      // 获取数据列表
      getDataList () {
        this.dataListLoading = true;
        this.$http({
          url: this.$http.adornUrl('/app/excleList'),
          method: 'get',
          params: this.$http.adornParams({
            'page': this.currentPage,
            'limit': this.pagesize
          })
        }).then((res) => {
          res = res.data;
          if (res.code == 0) {
            console.log(res.msg);
            this.tableData = res.page.list;
            this.currentPage = res.page.currPage;//默认开始页面
            this.pagesize = res.page.pageSize;//每页的数据条数
            this.total = res.page.totalCount;//默认数据总数
          } else {
            this.tableData = [];
            this.total = 0;
          }
          this.dataListLoading = false;
        })
      },
      // 当前页
      current_change (currentPage) {
        this.currentPage = currentPage;
        this.getDataList();
      },
      // 搜索
      lookupClick () {
        this.dataListLoading = true;
        this.$http({
          url: this.$http.adornUrl('/app/excleList'),
          method: 'get',
          params: this.$http.adornParams({
            'idCard': this.lookup
          })
        }).then((res) => {
          res = res.data;
          this.tableData = res.page.list;
          this.dataListLoading = false;
        })
      }
    }
  }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  .header-input {
    width: 150px;
  }

  .header-date {
    float: left;
    margin: 0 5px;
  }

  .header-date:first-child {
    margin-left: 0;
  }

  .tables {
    width: 98px;
    margin: 20px auto 0 auto;
  }

  .el-upload__tip {
    text-align: left;
  }

  .alert {
    width: 98%;
    margin-top: 20px;
  }

  .alert button {
    height: 30px;
    line-height: 5px;
    margin-left: 30%;
  }

  .alert a {
    text-decoration: none;
    color: #409EFF;
  }
  .Align{
    display:block;text-align:center;
  }
</style>
