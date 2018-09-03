<template>
  <div class="report-index">
      <div class="top">
        <div class="left">
          <el-button type="primary" icon="el-icon-plus" style="padding: 10px 20px;" @click="go2CreateReport">创建报表</el-button>
        </div>
        <div class="right">
            <el-input maxlength="100" placeholder="请输入IP地址或设备名称" v-model="key" size="medium">
              <i slot="prefix" class="el-input__icon el-icon-search"></i>
            </el-input>
        </div>
      </div>
      <div class="content" >
          <el-table
              :data="tableData"
              style="width: 100%;padding-top:10px;">
              <el-table-column
                prop="title"
                label="报表名称">
              </el-table-column>
              <el-table-column
                prop="address"
                label="图表布局">
                <template scope="scope">
                      {{ scope.row.layout_row }}行/{{ scope.row.layout_col }}列
                </template>
              </el-table-column>
              <el-table-column
                label="图表数量">
                <template scope="scope">
                      {{ scope.row.layout_row * scope.row.layout_col }}
                </template>
              </el-table-column>
              <el-table-column
                prop="description"
                label="报表描述">
              </el-table-column>
              <el-table-column
                label="操作">
                <template  scope="scope">
                  <el-button style="padding: 0px;" type="text" @click="go2Update(scope.row)">修改</el-button>
                  <el-button style="padding: 0px;" type="text" @click="go2Del(scope.row)">删除</el-button>
                  <el-button style="padding: 0px;" type="text" @click="go2Generate(scope.row)">生成代码</el-button>
                  <el-button style="padding: 0px;" type="text" @click="go2Preview(scope.row)">预览</el-button>
                </template>
              </el-table-column>
            </el-table>
            <div class="page">
              <rjPage :pageInit="1" :pageSizeInit="10"></rjPage>
            </div>
            
      </div>
      <rjDialog></rjDialog>
  </div>
</template>

<script type="text/javascript">
import rjPage from '@/components/common/page.vue'
import rjDialog from '@/components/common/dialog.vue'
import reportCreate from '@/components/report/reportCreate.vue'


export default {
  components:{
      rjDialog,
      rjPage,
      reportCreate
  },
  data () {
    return {
      key:"",
      tableData: []
    }
  },
  mounted() {
    this.initPage();
    this.findReports();
  },
  methods: {
    initPage() {
      let self = this;
      self.rjPage.
          handleSizeChange(function(val){
              self.findReports();
          }).
          handleCurrentChange(function(val){
              self.findReports();
          });
    },
    findReports(){
        let self = this;
        this.$axios
          .get("/v1/reports?key="+self.key+"&page_no="+self.rjPage.page()+"&page_size="+self.rjPage.pageSize())
          .then(function(response) {
              console.log(response)
              self.tableData = response.data.items;
              self.rjPage.total(response.data.total);
          }).catch(function(error) {
                console.log(error);
          });
    },
    go2CreateReport(){
      let self = this;
      self.rjDialog.
          title("新增报表").
          width("1000px").
          top().
          currentView(reportCreate, {deviceNum:5}).
          closeOnClickModal(false).
          showClose(true).
          sizeSelf('report-create').
          then(function(opt){
            self.findReports();
          }).
          show();
    },
    go2Update(row){
        let self = this;
        self.rjDialog.
            title("修改报表").
            width("1000px").
            top().
            currentView(reportCreate, {reportId:row.id}).
            closeOnClickModal(false).
            showClose(true).
            sizeSelf('report-create').
            then(function(opt){
              self.findReports();
            }).
            show();
    },
    go2Del(row){
      let self = this;
      self.$confirm('删除报表将无法恢复, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          self.$axios
            .delete("/v1/reports/"+row.id)
            .then(function(response) {
                self.findReports();
                self.$message({
                  showClose: true,
                  message: '删除报表成功',
                  type: 'success'
                });
            }).catch(function(error) {
               console.log(error);
            });
        }).catch(() => {
          self.$message({
            type: 'info',
            message: '已取消删除'
          });          
        });
      
    },
    go2Generate(row){

    },
    go2Preview(row){

    }

  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  .left {
    float: left;
  }
  .right {
    float: right;
  }
  
  .top {
    clear: both;
  }
  .page {
    float: right;
    margin-top: 5px;
  }
</style>
<style>
  .el-table th, .el-table__footer-wrapper thead div, .el-table__header-wrapper thead div {
      background: rgba(241, 241, 241, 1) !important;
  }

 .el-table th, .el-table__footer-wrapper thead, .el-table__header-wrapper thead {
      border-bottom: solid 1px rgb(215, 215, 215) !important;
      border-right: solid 1px rgb(215, 215, 215) !important;
  }
  .el-dialog__header {
  padding: 10px 10px 10px!important;
}

.el-dialog__headerbtn {
  top: 5px!important;
    right: 10px!important;
    font-size: 21px!important;
}
</style>
