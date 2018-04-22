<template>
  <page>
      <top title="实习总结列表" :showBack="true"/>
      <r-body>
              <search :condition="condition" :callBack="search"/>
              <card>
                  <selector  title="状态" :options="options" :model="this" value="status" :onChange="search"></selector>
              </card>
              <card>
                   <r-table :data="data" />
              </card>
             
      </r-body> 
  </page>
</template>

<script>
import {
  Page,
  RImage,
  RButton,
  RTextarea,
  Picker,
  Cell,
  Box,
  TabBar,
  DateTime,
  Grid,
  Card,
  RTable,
  RBody,
  Selector
} from "rainbow-mobile-core";
import Top from "../components/Top.vue";
import Util from "../util/util";
import  Search from '../components/Search.vue';
export default {
  components: {
    Top,
    Page,
    Card,
    Box,
    RButton,
    RTable,
    DateTime,
    Picker,
    RTextarea,
    TabBar,
    Cell,
    RBody,
    Search,
    Selector
  },
  data() {
    return {
      condition: {},
      status: 0,
      name: [],
      data:{
        "head":[
          [{'text':'姓名'},{'text':'状态'},{'text':'操作'}]
        ],
        "body":[]
      },
      options: [{ key: 0, value: "未批阅" }, { key: 1, value: "已批阅" }],
    };
  },
  methods: {
    async search(condition) {

                  const identityId = Util.getIdentityId(this);
                  const param = {"status":this.status,"classId":condition.class,"studentNos":condition.student_Nos,"startDateStr":condition.startDate,"endDateStr":condition.endDate,"pageNo":1,"pageSize":50} 
                  const status = await this.$http.post(`intern/summary/list`,param);
                  const status_data = [];
                  _.each(status.body,(student,index)=>{
                      status_data.push([{'text':student.studentName},{'text':student.state==1?'已批阅':'未批阅'},{'text':"查看","link":"/summary/detail?id="+student.id}])
                  })
                  this.data.body = status_data;
                  sessionStorage.setItem("summary_data",JSON.stringify(status_data));

    }
  },
   mounted(){
    const recordList = sessionStorage.getItem("summary_data");
    if(recordList){
        this.data.body = JSON.parse(recordList);
    }
  }
};
</script>


