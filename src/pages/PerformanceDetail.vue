<template>
  <page>
      <top title="实习评价详情" :showBack="true"/>
      <r-body>
             <r-input title="分数"  :max="100" :min="0"  :model="this" value="v_score" :isNumber="true"/>
             <r-textarea placeholder="请输入评价" :model="this" value="comments" :height="600" :max="600"></r-textarea>
      </r-body>
             <tab-bar>
                  <cell type="row" :vertical="true">
                                <cell v-if="!score">
                                  <box >
                                      <r-button :onClick="submit">提交</r-button>
                                  </box>
                                </cell>
                                
                    </cell>
              </tab-bar>
  </page>
</template>

<script>
import {
  Page,
  RImage,
  RButton,
  RTextarea,
  Selector,
  Cell,
  Box,
  TabBar,
  DateTime,
  Grid,
  Card,
  RTable,
  Selecter,
  RBody,
  ConfirmApi,
  RInput
} from "rainbow-mobile-core";
import Top from "../components/Top.vue";
import Vue from 'vue';
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
    Selector,
    RTextarea,
    TabBar,
    Cell,
    RBody,
    ConfirmApi,
    RInput
  },
  data() {
    return {
      comments: null,
      score:null,
      v_score:null,
      state:null
    };
  },
  methods: {
   
    async submit() {
                  const id = this.$route.query.id;
                  if(!this.v_score){
                      ConfirmApi.show(this,{
                            title: '',
                            content: '请输入成绩',
                          });
                  }else{
                       const studentNo = this.$route.query.studentNo;
                      const param = {"studentNo":studentNo,"score":this.v_score,"comments":this.comments,"state":1} 
                      const list = await this.$http.post(`intern/attendenceAppraisal/create`,param);
                      if(list.body){
                            ConfirmApi.show(this,{
                                title: '',
                                content: '操作成功',
                              });
                      }else{
                              ConfirmApi.show(this,{
                                title: '',
                                content: '操作失败',
                              });
                      }
                  }
                  
    },
  
  },
  async mounted(){
          const id = this.$route.query.id;
          if(id!='null'){
                  const url = "intern/attendenceAppraisal/detail?attendenceAppraisalId="+id;
                  const temp_record = await this.$http.get(url);
                  if(temp_record.body){
                    this.comments = temp_record.body.comments;
                    this.score = temp_record.body.score;
                    this.v_score = temp_record.body.score;
                    this.state = temp_record.body.state;
                  }
          }
  
  }
};
</script>


