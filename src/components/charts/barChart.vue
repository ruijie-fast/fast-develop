 <template>
  <div class="bar-chart">
      <div id="barChart" :style="{width: '300px', height: '300px'}"></div>
  </div>
</template>

<script>
export default {
  props: {
    xData:[Array],
    yData:[Array],
    title:[String],
    disable:{
      type:[Boolean],
      default () {
        return false
      }
    }
  },
  data () {
    return {
      
    }
  },
  mounted() {
    this.drawLine();
  },
  methods: {
     drawLine(){
        // 基于准备好的dom，初始化echarts实例
        let myChart = this.$echarts.init(document.getElementById('barChart'));
        // 绘制图表
        myChart.setOption({
            title: { text: '在Vue中使用echarts' },
            tooltip: {},
            xAxis: {
                data: ["衬衫","羊毛衫","雪纺衫","裤子","高跟鞋","袜子"]
            },
            series: [{
                name: '销量',
                type: 'bar',
                data: [5, 20, 36, 10, 10, 20]
            }]
        });
      }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  
</style>
