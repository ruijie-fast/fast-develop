<template>
  <div class="generate-index">
    <div class="top">
      <el-button type="primary" icon="el-icon-plus" style="padding: 10px 20px;" class="left" @click="go2CreateForm">创建新表单</el-button>
    </div>
    <div class="right">
      <el-input maxlength="100" placeholder="请输入表单名称/业务功能名" v-model="query.keyword" size="medium">
        <i slot="prefix" class="el-input__icon el-icon-search"></i>
      </el-input>
    </div>

    <div class="content" >
      <el-table
        :data="tableData"
        style="width: 100%;padding-top:10px;">
        <el-table-column
          prop="menuName"
          label="表单名/业务功能名" min-width="60">
        </el-table-column>
        <el-table-column
          prop="describe"
          label="功能说明" min-width="120">
        </el-table-column>
        <el-table-column
          prop="template"
          label="下发模版" min-width="60">
        </el-table-column>
        <el-table-column
          prop="moduleName"
          label="表单模型" min-width="40">
        </el-table-column>
        <el-table-column
          prop="tableName"
          label="关联表名" min-width="80">
        </el-table-column>
        <el-table-column
          label="操作" min-width="80">
          <template  scope="scope">
            <el-button style="padding: 0px;" type="text" @click="go2Update(scope.row)">修改</el-button>
            <el-button style="padding: 0px;" type="text" @click="go2Del(scope.row)">删除</el-button>
            <el-button style="padding: 0px;" type="text" @click="go2Generate(scope.row)">生成代码</el-button>
          </template>
        </el-table-column>
      </el-table>
      <div class="page">
        <rjPage :pageInit="1" :pageSizeInit="10"></rjPage>
      </div>

    </div>
    <rjDialog></rjDialog>
    <rjConfirm></rjConfirm>
  </div>
</template>

<script type="text/javascript">
  import rjPage from '@/components/common/page.vue'
  import rjDialog from '@/components/common/dialog.vue'
  import rjConfirm from '@/components/common/confirm.vue'
  import formCreate from '@/components/generate/formCreate.vue'

export default {
  components:{
    rjDialog,
    rjPage,
    rjConfirm,
    formCreate
  },
  data () {
    return {
      query: {
        keyword:"",
        page_no:'1',
        page_size:'10'
      },
      tableData: []
    }
  },
  mounted() {
    this.initPage();
    this.searchForms();
  },
  computed: {
    keyword() {
      return this.query.keyword
    }
  },
  methods: {
    initPage() {
      let self = this;
      self.rjPage.
      handleSizeChange(function(val){
        self.searchForms();
      }).
      handleCurrentChange(function(val){
        self.searchForms();
      });
    },
    searchForms(){
      let self = this;
      let sysGen = {};
      sysGen.tableName = self.query.keyword;
      sysGen.page = self.query.page_no;
      sysGen.pageSize = self.query.page_size;
      this.$axios
        .post("/v1/sys/gen/qryPage", sysGen)
        .then(function(response) {
          if (response.data.responeCode == '0') {
            self.tableData = response.data.data.list;
            self.rjPage.total(response.data.data.total);
          } else {
            self.$message.error("查询失败");
          }
        }).catch(function(error) {
          console.log(error);
        });
    },
    go2CreateForm(){
      let self = this;
      self.rjDialog.
      title("新增表单").
      width("1200px").
      top("3%").
      currentView(formCreate,{}).
      closeOnClickModal(false).
      showClose(true).
      sizeSelf('form-create').
      then(function(opt){
        self.searchForms();
      }).
      show();
    },
    go2Update(row){
      let self = this;
      self.rjDialog.
      title("修改表单").
      width("1200px").
      top("3%").
      currentView(formCreate).
      closeOnClickModal(false).
      showClose(true).
      sizeSelf('form-create').
      then(function(opt){
        self.searchForms();
      }).
      show();
    },
    go2Del(row){
      let self = this;
      self.$confirm('确认删除所选表单?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        /*self.$axios.delete("placeHolder")
          .then(function(response) {
            if(!response.data.success){
              self.rjMessage.error(response.data.message);
              return;
            } else {
              self.$message.success("删除表单成功！")
              self.searchForms();
            }
          })
          .catch(function(error) {
            console.log(error)
          });*/
        self.$message.success("删除表单成功！")
        self.searchForms();
      })
    },
    go2Generate(row){

    },
  },
  watch: {
    keyword(curVal, oldVal){
      if (curVal != oldVal) {
        this.searchForms();
      }
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
