<template>
  <page>
      <top title="查看共享" :showBack="true"/>
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
       data:{
        "head":[
          [{'text':'姓名'},{'text':'地点'}]
        ],
        "body":[]
      },
      condition:{},
      options: [{ key: 0, value: "未响应" }, { key: 1, value: "已响应" }],
      showFlag:false,
      toastText:"",
      status:0,
      showDialog:false
    };
  },
  computed :{
    
  },
  methods:{
    async search(condition){
                  const identityId = Util.getIdentityId(this);
                  const param = {"status":this.status,"identityId":identityId,"classId":condition.class,"studentNos":condition.student_Nos,"startDateStr":condition.startDateStr,"endDateStr":condition.endDateStr,"pageNo":1,"pageSize":30} 
                  const list = await this.$http.post(`location/sharing/list`,param);
                  this.data.body = _.map(list.body,(s)=>{
                        return [{'text':s.studentName},{'text':s.sharedAddress?s.sharedAddress:"未响应"}]
                  })
    }
  },
  
};
</script>


