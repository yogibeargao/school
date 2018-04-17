<template>
  <page>
      <top title="学生记录" :showBack="true"/>
        <r-body>
                <search :condition="condition" :callBack="search"/>
                <card>
                  <selector  title="状态" :options="options" :model="this" value="status" :onChange="search"></selector>
                </card>
                <card>
                      <r-table :data="data" />
                </card>
                <card v-if="!this.status">
                  <r-textarea placeholder="请输入实习评价" :model="this" value="commons" :height="600" :max="600"></r-textarea>
                </card>
        </r-body>
              <tab-bar v-if="!this.status">
                <cell type="row" :vertical="true">
                              <cell >
                                  <box >
                                      <r-button link="/record/evaluate">全部评价</r-button>
                                  </box>
                              </cell>
                  </cell>
             </tab-bar>
  </page>
</template>

<script>
import { Page, RImage,RBody, RButton,TabBar,Picker, Cell, Box, DateTime,Grid,Card,RTable,Selector,RTextarea } from "rainbow-mobile-core";
import Top from '../components/Top.vue';
import Search from '../components/Search.vue';
import Util from "../util/util";

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
    RBody,
    Search,
    Selector,
    RTextarea
  },
  data() {
    return {
       data:{
        "head":[
          [{'text':'姓名'},{'text':'状态'},{'text':'操作'}]
        ],
        "body":[]
      },
      condition:{},
      options: [{ key: 0, value: "未评价" }, { key: 1, value: "已评价" }],
      status:1,
      commons:""
    };
  },
  methods:{
      async search(condition){
                  const identityId = Util.getIdentityId(this);
                  const param = {"status":this.status,"identityId":identityId,"classId":condition.class,"studentNos":condition.student_Nos,"startDateStr":condition.startDate,"endDateStr":condition.endDate,"pageNo":1,"pageSize":30} 
                  const list = await this.$http.post(`intern/detail/list`,param);
                  
                  this.data.body = _.map(list.body,(s)=>{
                        return [{'text':s.studentName},{'text':s.apprisal?'以评价':"未评价"},{'text':"查看","link":"/record/detail?id="+s.id}]
                  })
                  sessionStorage.setItem("recordList",JSON.stringify(this.data.body));
    }
  },
  mounted(){
    const recordList = sessionStorage.getItem("recordList");
    if(recordList){
        this.data.body = JSON.parse(recordList);
    }
  }
};
</script>


