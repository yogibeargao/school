<template>
  <page>
      <top title="实习表现" :showBack="true"/>
      <r-body>
           <card title="实习评价">
                  <r-input title="分数:" :isReadOnly="true"  :max="100" :min="0"  :model="this" value="v_score" :isNumber="true"/>
                  <r-textarea title="评价:" :isReadOnly="true" :model="this" value="comments" :height="600" :max="600"></r-textarea>
            </card>
            <card title="请假表现">
                  <div id="ill" style="width: 100%;height:270px;"></div>
            </card>
            <card title="记录表现">
                  <div id="record" style="width: 100%;height:270px;"></div>
            </card>
      </r-body>
           
          
  </page>
</template>

<script>
import { Page, RBody,RImage,RInput,RButton,RTextarea, Cell, Box, MenuBar,TabBar,Card,RTable } from "rainbow-mobile-core";
import  Top from '../components/Top.vue';
export default {
  components: {
    Top,
    Page,
    Card,
    Box,
    RButton,
    RTable,
    TabBar,
    Cell,
    RTextarea,
    RBody,
    RInput
  },
  data(){
      return {
          comments:null,
          v_score:null
      }
  },
  async mounted (){
        const myChart = echarts.init(document.getElementById('ill'));
        const option = {
            title: {
                text: 'ECharts 入门示例'
            },
            tooltip: {},
            legend: {
                data:['销量']
            },
            xAxis: {
                data: ["衬衫","羊毛衫","雪纺衫","裤子","高跟鞋","袜子"]
            },
            yAxis: {},
            series: [{
                name: '销量',
                type: 'bar',
                data: [5, 20, 36, 10, 10, 20]
            }]
        };
        myChart.setOption(option);
        const record = echarts.init(document.getElementById('record'));
        const recordOption = {
    tooltip: {
        trigger: 'item',
        formatter: "{a} <br/>{b}: {c} ({d}%)"
    },
    legend: {
        orient: 'vertical',
        x: 'left',
        data:['直接访问','邮件营销','联盟广告','视频广告','搜索引擎']
    },
    series: [
        {
            name:'访问来源',
            type:'pie',
            radius: ['50%', '70%'],
            avoidLabelOverlap: false,
            label: {
                normal: {
                    show: false,
                    position: 'center'
                },
                emphasis: {
                    show: true,
                    textStyle: {
                        fontSize: '30',
                        fontWeight: 'bold'
                    }
                }
            },
            labelLine: {
                normal: {
                    show: false
                }
            },
            data:[
                {value:335, name:'直接访问'},
                {value:310, name:'邮件营销'},
                {value:234, name:'联盟广告'},
                {value:135, name:'视频广告'},
                {value:1548, name:'搜索引擎'}
            ]
        }
    ]
};

        record.setOption(recordOption);
          const url = "intern/attendenceAppraisal/stu";
                  const temp_record = await this.$http.get(url);
                  if(temp_record.body){
                    this.comments = temp_record.body.comments;
                    this.v_score = temp_record.body.score;
                  }
  }
 
};
</script>


