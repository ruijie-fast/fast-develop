<template>
  <div class="form-create">
    <rj-dialog-contant>
      <div slot="content">
        <el-steps :active="stepActive" finish-status="success" align-center>
          <el-step title="基本信息">
          </el-step>
          <el-step title="规则配置">
          </el-step>
          <el-step title="生成代码">
          </el-step>
        </el-steps>
        <div v-show="stepActive == 0" class="basic-info">
          <el-form ref="basicForm" :model="basicForm" label-width="100px">
            <el-form-item class="form-item half" label="表名">
              <el-select style="width:280px" v-model="basicForm.tableName" placeholder="请选择">
                <el-option v-for="item in tables" :key="item.value" :label="item.label" :value="item.value">
                </el-option>
              </el-select>
            </el-form-item>
            <el-form-item class="form-item half" label="生成模版">
              <el-select style="width:280px" v-model="basicForm.template" placeholder="请选择">
                <el-option v-for="item in generateTypes" :key="item.value" :label="item.label" :value="item.value">
                </el-option>
              </el-select>
            </el-form-item>
            <el-form-item class="form-item half" label="模块包名">
              <el-input style="width:280px" v-model="basicForm.packageName"></el-input>
            </el-form-item>
            <el-form-item class="form-item half" label="菜单名称">
              <el-input style="width:280px" v-model="basicForm.menuName"></el-input>
            </el-form-item>
            <el-form-item class="form-item half" label="类名">
              <el-input style="width:280px" v-model="basicForm.className"></el-input>
            </el-form-item>
            <el-form-item class="form-item half" label="开发者">
              <el-input style="width:280px" v-model="basicForm.author"></el-input>
            </el-form-item>
          </el-form>
          <div class="clearfix"></div>
        </div>
        <div v-show="stepActive == 1" class="rule-info">
          <el-table
            :data="ruleData" highlight-current-row
            style="width: 100%;margin-top:10px; max-height:480px;overflow:auto">
            <el-table-column label="字段" align="center">
              <el-table-column
                prop="columnName"
                label="列名"
                show-overflow-tooltip
                fit="true"
                min-width="160">
                <template scope="scope">
                  {{scope.row.columnName}}
                </template>
              </el-table-column>
              <el-table-column
                prop="describe"
                label="说明"
                show-overflow-tooltip
                fit="true"
                min-width="200">
                <template scope="scope">
                  <el-input v-model="scope.row.describe" @change="handleEdit(scope.$index, scope.row)"></el-input>
                </template>
              </el-table-column>
              <el-table-column
                prop="physicalType"
                label="物理类型"
                show-overflow-tooltip
                fit="true"
                min-width="140">
                <template scope="scope" >
                  {{scope.row.physicalType}}
                </template>
              </el-table-column>
              <el-table-column
                prop="javaType"
                label="Java类型"
                show-overflow-tooltip
                fit="true"
                min-width="140">
                <template scope="scope">
                  <el-select v-model="scope.row.javaType" placeholder="请选择">
                    <el-option v-for="item in javaTypes" :key="item.value" :label="item.label" :value="item.value">
                    </el-option>
                  </el-select>
                </template>
              </el-table-column>
              <el-table-column
                prop="javaProp"
                label="Java属性名称"
                show-overflow-tooltip
                fit="true"
                min-width="160">
                <template scope="scope">
                  <el-input v-model="scope.row.javaProp" @change="handleEdit(scope.$index, scope.row)"></el-input>
                </template>
              </el-table-column>
              <el-table-column
                prop="primaryKey"
                label="主键"
                show-overflow-tooltip
                fit="true"
                min-width="50"
                align="center">
                <template scope="scope">
                  <el-checkbox v-model="scope.row.primaryKey">
                  </el-checkbox>
                </template>
              </el-table-column>
              <el-table-column
                prop="nullable"
                label="可空"
                show-overflow-tooltip
                fit="true"
                min-width="50"
                align="center">
                <template scope="scope">
                  <el-checkbox v-model="scope.row.nullable">
                  </el-checkbox>
                </template>>
              </el-table-column>
              <el-table-column
                prop="insertable"
                label="插入"
                show-overflow-tooltip
                fit="true"
                min-width="50"
                align="center">
                <template scope="scope">
                  <el-checkbox v-model="scope.row.insertable">
                  </el-checkbox>
                </template>
              </el-table-column>
              <el-table-column
                prop="editable"
                label="编辑"
                show-overflow-tooltip
                fit="true"
                min-width="50"
                align="center">
                <template scope="scope">
                  <el-checkbox v-model="scope.row.editable">
                  </el-checkbox>
                </template>
              </el-table-column>
            </el-table-column>
            <el-table-column label="列表" align="center">
              <el-table-column
                prop="listable"
                label="列表"
                show-overflow-tooltip
                fit="true"
                min-width="50"
                align="center">
                <template scope="scope">
                  <el-checkbox v-model="scope.row.listable">
                  </el-checkbox>
                </template>>
              </el-table-column>
              <el-table-column
                prop="queryable"
                label="查询"
                show-overflow-tooltip
                fit="true"
                min-width="50"
                align="center">
                <template scope="scope">
                  <el-checkbox v-model="scope.row.queryable">
                  </el-checkbox>
                </template>
              </el-table-column>&ndash;&gt;
              <el-table-column
                prop="matchType"
                label="匹配方式"
                show-overflow-tooltip
                fit="true"
                min-width="160">
                <template scope="scope">
                  <el-select v-model="scope.row.matchType" placeholder="请选择">
                    <el-option v-for="item in matchTypes" :key="item.value" :label="item.label" :value="item.value">
                    </el-option>
                  </el-select>
                </template>
              </el-table-column>
            </el-table-column>
            <el-table-column label="表单" align="center">
              <el-table-column
                prop="formType"
                label="表单类型"
                show-overflow-tooltip
                fit="true"
                min-width="160">
                <template scope="scope">
                  <el-select v-model="scope.row.formType" filterable placeholder="请选择">
                    <el-option v-for="item in formTypes" :key="item.value" :label="item.label" :value="item.value">
                    </el-option>
                  </el-select>
                </template>
              </el-table-column>
              <el-table-column
                prop="dictType"
                label="字典类型"
                show-overflow-tooltip
                fit="true"
                min-width="160">
                <template scope="scope">
                  <el-input v-model="scope.row.dictType" @change="handleEdit(scope.$index, scope.row)"></el-input>
                </template>
              </el-table-column>
              <el-table-column
                prop="order"
                label="排序"
                show-overflow-tooltip
                fit="true"
                min-width="70">
                <template scope="scope">
                  <el-input v-model="scope.row.order" @change="handleEdit(scope.$index, scope.row)"></el-input>
                </template>
              </el-table-column>
            </el-table-column>
          </el-table>
        </div>
        <div v-show="stepActive == 3" class="success-info">
          <img class="success-icon" src="/static/image/success.png"/>
          <div style="margin-bottom: 10px"><span class="success-result">生成成功</span></div>
          <div style="margin-bottom: 30px"><span class="success-tip">代码已生成成功，可以根据代码覆盖到项目当中去</span></div>
        </div>
      </div>
      <div slot="footer-opt" style="text-align:center;">
        <el-button id="save-btn" v-show="stepActive == 1" @click="preStep">上一步</el-button>
        <el-button type="primary" v-show="stepActive == 0" id="save-btn2" @click="nextStep">下一步</el-button>
        <el-button type="primary" v-show="stepActive == 1" :load="generateLoading" id="save-btn3" @click="go2Generate">生成代码</el-button>
        <el-button v-show="stepActive < 2" id="save-btn4">暂存</el-button>
        <el-button v-show="stepActive == 3" id="download" type="primary" @click="go2Download">下载代码</el-button>
        <el-button v-show="stepActive == 3" id="returnPage" @click="returnPage">返回首页</el-button>
      </div>
    </rj-dialog-contant>
  </div>
</template>

<script type="text/javascript">
  import rjDialogContant from '@/components/common/dialogContant.vue'

  export default {
    components:{
      rjDialogContant
    },
    data () {
      return {
        stepActive: 0,
        queryKey:undefined,
        generateLoading:false,
        basicForm: {
          id: "",
          tableName: "",
          template: "",
          packageName: "",
          menuName: "",
          className: "",
          author: ""
        },
        tables:[
          {
            value: 1,
            label: "商品表"
          },
          {
            value: 2,
            label: "订单表"
          }
        ],
        generateTypes: [
          {
            value: 1,
            label: '增删改查'
          }
        ],
        ruleData: [
          {
            "columnName": "id",
            "describe": "编号",
            "physicalType": "varchar(30)",
            "javaType": 1,
            "javaProp": "id",
            "primaryKey": true,
            "nullable": false,
            "insertable": true,
            "editable": false,
            "listable": false,
            "queryable": false,
            "matchType": 1,
            "formType": 1,
            "dictType": "",
            "order": 10,
          },
          {
            "columnName": "order_id",
            "describe": "所属销售订单",
            "physicalType": "varchar(30)",
            "javaType": 1,
            "javaProp": "order_id",
            "primaryKey": false,
            "nullable": true,
            "insertable": true,
            "editable": true,
            "listable": false,
            "queryable": false,
            "matchType": 1,
            "formType": 1,
            "dictType": "",
            "order": 20,
          },
          {
            "columnName": "product_id",
            "describe": "商品",
            "physicalType": "varchar(30)",
            "javaType": 1,
            "javaProp": "product_id|name",
            "primaryKey": false,
            "nullable": false,
            "insertable": true,
            "editable": true,
            "listable": true,
            "queryable": false,
            "matchType": 1,
            "formType": 1,
            "dictType": "",
            "order": 30,
          },
          {
            "columnName": "unit_type",
            "describe": "单位",
            "physicalType": "varchar(30)",
            "javaType": 1,
            "javaProp": "unitType",
            "primaryKey": false,
            "nullable": true,
            "insertable": true,
            "editable": true,
            "listable": true,
            "queryable": false,
            "matchType": 1,
            "formType": 1,
            "dictType": "",
            "order": 40,
          },
          {
            "columnName": "price",
            "describe": "单价(元)",
            "physicalType": "decimal(12,2)",
            "javaType": 6,
            "javaProp": "price",
            "primaryKey": false,
            "nullable": true,
            "insertable": true,
            "editable": true,
            "listable": true,
            "queryable": false,
            "matchType": 1,
            "formType": 1,
            "dictType": "",
            "order": 50,
          },
          {
            "columnName": "num",
            "describe": "数量",
            "physicalType": "int(11)",
            "javaType": 2,
            "javaProp": "num",
            "primaryKey": false,
            "nullable": true,
            "insertable": true,
            "editable": true,
            "listable": true,
            "queryable": false,
            "matchType": 1,
            "formType": 1,
            "dictType": "",
            "order": 60,
          },
          {
            "columnName": "amount",
            "describe": "金额(元)",
            "physicalType": "decimal(12,2)",
            "javaType": 6,
            "javaProp": "amount",
            "primaryKey": false,
            "nullable": true,
            "insertable": true,
            "editable": true,
            "listable": true,
            "queryable": false,
            "matchType": 1,
            "formType": 1,
            "dictType": "",
            "order": 70,
          },
          {
            "columnName": "remarks",
            "describe": "备注",
            "physicalType": "varchar(50)",
            "javaType": 1,
            "javaProp": "remarks",
            "primaryKey": false,
            "nullable": true,
            "insertable": true,
            "editable": true,
            "listable": true,
            "queryable": false,
            "matchType": 1,
            "formType": 1,
            "dictType": "",
            "order": 80,
          },
          {
            "columnName": "del_flag",
            "describe": "删除标记",
            "physicalType": "char(1)",
            "javaType": 1,
            "javaProp": "delFlag",
            "primaryKey": false,
            "nullable": true,
            "insertable": true,
            "editable": false,
            "listable": false,
            "queryable": false,
            "matchType": 1,
            "formType": 1,
            "dictType": "del_flag",
            "order": 90,
          }
        ],
        javaTypes: [
          {
            value: 1,
            label: 'String'
          }, {
            value: 2,
            label: 'Integer'
          }, {
            value: 3,
            label: 'Float'
          }, {
            value: 4,
            label: 'Double'
          }, {
            value: 5,
            label: 'BigInteger'
          }, {
            value: 6,
            label: 'BigDecimal'
          }
        ],
        matchTypes: [
          {
            value: 1,
            label: '='
          }, {
            value: 2,
            label: '>'
          }, {
            value: 3,
            label: '<'
          }, {
            value: 4,
            label: '>='
          }, {
            value: 5,
            label: '<='
          }, {
            value: 6,
            label: 'in'
          }
        ],
        formTypes: [
          {
            value: 1,
            label: '单行文本'
          }, {
            value: 2,
            label: '多行文本'
          }, {
            value: 3,
            label: '下拉选框'
          }, {
            value: 4,
            label: '单选按钮'
          }, {
            value: 5,
            label: '复选框'
          }, {
            value: 6,
            label: '日期选择'
          }
        ],
      }
    },
    mounted() {
      let self = this;
      if (null != self.rjDialogParams().id) {
        self.basicForm.id = self.rjDialogParams().id;
      }
    },
    methods: {
      nextStep(){
        let self = this;
        self.stepActive = self.stepActive + 1;
      },
      preStep(){
        let self = this;
        if(self.stepActive >0) {
          self.stepActive = self.stepActive - 1;
        }
      },
      initPage() {
        let self = this;
      },
      go2Generate() {
        let self = this;
        let formVos = {};
        self.generateLoading = true;
        formVos.basicData = self.basicForm;
        formVos.ruleData = self.ruleData;
        /*self.$axios.post("placeholder", formVos)
          .then(function(response) {
            var message = response.data.message;
            if(message.isSuccess) {
              self.stepActive = 3;
            } else {
              self.$message({
                type: "error",
                message: "代码生成失败！"
              })
            }
          })
          .catch(function(error) {
            console.log(error);
          });*/
        self.generateLoading = false;
        self.stepActive = 3;
      },
      go2Download() {
        console.log("download");
      },
      returnPage() {
        let self = this;
        self.closeRjDialog();
      }
    }
  }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  .basic-info {
    margin-top: 20px;
  }
  .form-item {
    display: block;
    float: left;
  }
  .half {
    padding-left:5%;
    width:40%
  }
  .clearfix {
    clear: both;
  }
  .success-icon {
    margin: 10px 0px;
  }
  .success-info {
    margin: auto;
    text-align: center;
  }
  .success-result {
    font-size: 24px;
    font-weight: bold;
  }

</style>
