<template>
  <page>
      <top title="学生成绩" :showBack="true"/>
      <r-body>
              <search :condition="condition" :callBack="search" :showTime="false"/>
              
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
  RDialog,
  RBody,
  Selector,
  Checker,
  Row
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
    Selector,
    RDialog,
    Checker,
    Row,
    Search
  },
  data() {
    return {
      showDialog: false,
      condition: {},
      v_student: null,
      class: null,
      classes:[],
      student:null,
      data:{
        "head":[
          [{'text':'姓名'},{'text':'分数'},{'text':'操作'}]
        ],
        "body":[]
      },

    };
  },
  methods: {
     async search(condition){
                  const param = {"classId":condition.class,"studentNos":condition.student_Nos,"pageNo":1,"pageSize":30};
                  const scores = await this.$http.post(`intern/score/list`,param);
                  const scores_data = [];
                  _.each(scores.body,(score,index)=>{
                      scores_data.push([{'text':score.studentName},{'text':score.schoolScore},{'text':'打分','link':"/score/detail?id="+score.id}])
                  })
                  this.data.body = scores_data;
                  sessionStorage.setItem("scores_data",JSON.stringify(scores_data));

    },
  },
   async mounted(){
                  const _scores_data = sessionStorage.getItem("scores_data");
                  if(_scores_data){
                    this.data.body = JSON.parse(_scores_data);
                  }
  }
};
</script>


