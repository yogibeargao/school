<template>
  <page>
      <top title="签到查看" :showBack="true"/>
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
import { Page,RDialog,Toast,Row,Checker, RImage,RBody,Confirm, RButton,TabBar,Picker, Cell, Box, DateTime,Grid,Card,RTable,Selector } from "rainbow-mobile-core";
import  Top from '../components/Top.vue';
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
    TabBar,
    Cell,
    Picker,
    DateTime,
    RBody,
    Selector,
    Confirm,
    RDialog,
    Row,
    Checker,
    Toast,
    Search
  },
  data() {
    return {
       condition:{},
       status:0,
       options: [{ key: 0, value: "未签到" }, { key: 1, value: "已签到" }],
        data:{
        "head":[
          [{'text':'姓名'},{'text':'位置'},{'text':'时间'}]
        ],
        "body":[]
      }
    };
  },
  computed :{
      disableButton(){
        return _.isEmpty(this.student);
      }
    
  },
  methods:{
   async search(condition){
                  const identityId = Util.getIdentityId(this);
                  const param = {"status":this.status,"identityId":identityId,"classId":condition.class,"studentNos":condition.student_Nos,"startDateStr":condition.startDateStr,"endDateStr":condition.endDateStr,"pageNo":1,"pageSize":50} 
                  const status = await this.$http.post(`online/signin/list`,param);
                  const status_data = [];
                  _.each(status.body,(student,index)=>{
                      status_data.push([{'text':student.studentName},{'text':student.signAddress?student.signAddress:'未签到'},{'text':student.signDate?student.signDate.substring(11,16):''}])
                  })
                  this.data.body = status_data;
                  // sessionStorage.setItem("class",this.class);
                  // sessionStorage.setItem("student_Nos",JSON.stringify(student_Nos));
                  // sessionStorage.setItem("status_data",JSON.stringify(status_data));
          }
  }
};
</script>


