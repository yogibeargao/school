<template>
  <page>
      <top title="请假列表" :showBack="true"/>
<r-body>
              
               <search :condition="condition" :callBack="flash" :showClass="false"/>

               <card>
                  <selector  title="状态" :options="options" :model="this" value="type" :onChange="flash"></selector>
              </card>
                <card>
                      <r-table :data="data" />
                </card>
</r-body>   
  <tab-bar>
                <cell type="row" :vertical="true" v-if="isStudent">
                              <cell>
                                  <box>
                                      <r-button link='/ill/detail?type=submit'>请假申请</r-button>
                                  </box>
                              </cell>
                  </cell>
            </tab-bar>  
  </page>
</template>

<script>
import { Page,RBody, RImage, RButton,TabBar,Picker, Cell,Selector, Box, DateTime,Grid,Card,RTable } from "rainbow-mobile-core";
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
    DateTime,
    Cell,
    Picker,
    TabBar,
    Selector,
    RBody,
    Search
  },
  data() {
    return {
      data:{
        "head":[
          [{'text':'学生姓名'},{'text':'请假时间'},{'text':'状态'}]
        ],
        "body":[]
      },
      condition:{},
      type:null,
      options: [{ key: 0, value: "未审批" }, { key: 1, value: "已审批" }],
      class:[],
      name:[],
      flag:false
    };
  },
  computed:{
    isStudent(){
      return Util.isStudent(this);
    }
  },
  methods:{
      goto(){
        window.location.hash='/record/evaluate'
      },
      flash(){
         this.loadLeave();
         this.flag=!this.flag;
      },
      async loadLeave(){
            this.user = JSON.parse(sessionStorage.getItem("user"));
            const identityId = Util.getIdentityId(this);
            const url = `leave/list`;
            let param = {"pageNo":1,"pageSize":20};
            if(this.isStudent){
              param["studentNos"] = [identityId];
            }else{
              param["identityId"] = identityId;
            }
            if(this.type!=null){
              param["status"] = this.type;
            }else{
              param["status"] = "0"
            }
            if(this.condition.startDateStr!=null){
              param["startDateStr"] = this.condition.startDateStr+":00";
            }
            if(this.condition.endDateStr!=null){
              param["endDateStr"] = this.condition.endDateStr+":00";
            }
            const leaves = await this.$http.post(url,param);
            const loadLeaves = [];
            _.each(leaves.body,(leave)=>{
              leave.leaveStartDate = leave.leaveStartDate?leave.leaveStartDate.substring(0,16):"";
              leave.leaveEndDate = leave.leaveEndDate?leave.leaveEndDate.substring(0,16):"";
              loadLeaves.push([{'text':leave.studentName},{'text':leave.leaveStartDate},{'text':leave.state==1?'已审批':'未审批','link':'/ill/detail?leaveId='+leave.leaveId}])
            })
            if(!_.isEmpty(leaves.body)){
              sessionStorage.setItem("leaves",JSON.stringify(loadLeaves));
            }
            this.data.body = loadLeaves;
      }
  },
  mounted(){
       const leaves =  sessionStorage.getItem("leaves");
       if(leaves){
        this.data.body = JSON.parse(leaves);
       }

  }
};
</script>


