<template>
  <page>
      <top title="实习评价列表" :showBack="true"/>
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
import Search from '../components/Search.vue';
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
      status:0,
      condition: {},
      options: [{ key: 0, value: "未评价" }, { key: 1, value: "已评价" }],
      data:{
        "head":[
          [{'text':'姓名'},{'text':'班级'},{'text':'状态'}]
        ],
        "body":[]
      },

    };
  },
   methods: {
    async search(condition) {

                  const identityId = Util.getIdentityId(this);
                  const param = {"status":this.status,"classId":condition.class,"studentNos":condition.student_Nos,"startDateStr":condition.startDate,"endDateStr":condition.endDate,"pageNo":1,"pageSize":30} 
                  const status = await this.$http.post(`intern/attendenceAppraisal/list`,param);
                  const status_data = [];
                  console.log(status.body)
                  _.each(status.body,(student,index)=>{
                      status_data.push([{'text':student.studentName},{'text':student.signAddress?student.signAddress:'未评价'}])
                  })
                  this.data.body = status_data;
                  sessionStorage.setItem("appraisal_data",JSON.stringify(status_data));

    }
  },
   mounted(){
    const recordList = sessionStorage.getItem("appraisal_data");
    if(recordList){
        this.data.body = JSON.parse(recordList);
    }
  }
};
</script>


