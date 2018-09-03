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
              let myChart = self.$echarts.init(document.getElementById(''+chartId),"macarons");
              let chartType = response.data.type;
              if(chartType === 'pie'){
                  let series = [];
                  response.data.y_data.forEach(function(item){
                      let obj = {};
                      obj.name = item.legend;
                      obj.type = item.type;
                      obj.radius = "55%";
                      obj.center = ['50%', '60%'];
                      let dataArray = [];
                      item.y_data.forEach(function(val,index){
                          let data = {};
                          data.value = val;
                          data.name = response.data.x_data[index];
                          dataArray.push(data);
                      });
                      obj.data = dataArray;
                      series.push(obj);
                  });
                  // 绘制图表
                  myChart.setOption({
                  title: { text: response.data.title, x:'center' },
                  legend: {
                      orient : 'vertical',
                      x : 'left',
                      data: response.data.x_data
                  },
                  tooltip: {
                    trigger: 'item',
                    formatter: "{a} <br/>{b} : {c} ({d}%)"
                  },
                  toolbox: {
                      show : true,
                      feature : {
                          mark : {show: true},
                          dataView : {show: true, readOnly: false},
                          restore : {show: true},
                          saveAsImage : {show: true}
                      }
                  },
                  calculable : true,
                  series: series
                  });
              } else {
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
              }
              
          }).catch(function(error) {
                console.log(error);
          });
     },
     setChartOption(chartObj,chartType,response){
        
        
     }

  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  
</style>
