<template>
  <div class="preview">
      <el-row :gutter="20" v-for="(item,index) in row">
          <el-col :span="24 / col" v-for="(it,inde) in col">
              <div :id="'chartId'+index+inde" :style="{width: '100%', height: '500px'}"></div>
          </el-col>
      </el-row>
  </div>
</template>

<script>
export default {
  data () {
    return {
      col:1,
      row:1,
      reportId:0
    }
  },
  mounted() {
    this.reportId = this.$route.query.reportId;
    this.findReportChart();
  },
  methods: {
     findReportChart(){
        let self = this;
        this.$axios
          .get("/v1/reports/"+self.reportId)
          .then(function(response) {
              console.log(response)
              self.col = response.data.layout_col;
              self.row = response.data.layout_row;
              let chartLen = response.data.report_chart_vos.length;
              let chartItems = response.data.report_chart_vos;
              let chart = 0;
              if(chartLen > 0){
                  for(let i = 0;i < self.row; i++) {
                      for(let j = 0;j < self.col; j++) {
                          self.findChartData(chartItems[chart],"chartId"+i+j);
                          chart++;
                      }
                  }
              }
          }).catch(function(error) {
                console.log(error);
          });
     },
     findChartData(config,chartId){
        let self = this;
        this.$axios
          .post("/v1/reports/charts",config)
          .then(function(response) {
              console.log(response)
              // 基于准备好的dom，初始化echarts实例
              let myChart = self.$echarts.init(document.getElementById(''+chartId));
              let series = [];
              response.data.y_data.forEach(function(item){
                  let obj = {};
                  obj.name = item.legend;
                  obj.type = item.type;
                  obj.data = item.y_data;
                  series.push(obj);
              });
              // 绘制图表
              myChart.setOption({
              title: { text: response.data.title },
              legend: {
                  data: response.data.legend
              },
              tooltip: {trigger: 'axis'},
              xAxis: {
                  data: response.data.x_data
              },
              yAxis: {},
              series: series
              });
          }).catch(function(error) {
                console.log(error);
          });
     }

  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  
</style>
