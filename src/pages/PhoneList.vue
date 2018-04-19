<template>
  <page>
      <top title="手机审批" :showBack="true"/>
      <r-body>
                                  <search :condition="condition" :callBack="search" :showTime="false"/>
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
import { Page, RBody,RImage, RButton,TabBar,Picker, Cell, Box, DateTime,Grid,Card,RTable,Selector,Checker,Row,RDialog,ConfirmApi } from "rainbow-mobile-core";
import  Top from '../components/Top.vue';
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
    TabBar,
    Cell,
    Picker,
    DateTime,
    RBody,
    Selector,
    Checker,
    Row,
    RDialog,
    ConfirmApi,
    Search
  },
  data() {
    return {
      condition:{},
        data:{
        "head":[
          [{'text':'姓名'},{'text':'状态'},{'text':'同意'},{'text':'拒绝'}]
        ],
        "body":[]
      },
      options: [{ key: 0, value: "未审批" }, { key: 1, value: "已审批" }],
      startDate:null,
      name:[],
      students:[],
      v_student:[],
      phone:{},
      show:false,
      showFlag:false,
      toastText:"",
      status:0,
      showDialog:false
    };
  },
  methods :{
    async search(condition){
                  const param = {"status":this.status,"studentNos":condition.student_Nos,"pageNo":1,"pageSize":30};
                  const phones = await this.$http.post(`user/changephone/list`,param);
                  if(_.size(phones.body)>0){
                      const phone_data = [];
                      _.each(phones.body,(student,index)=>{
                          phone_data.push([{'text':student.studentName},{'text':student.status==0?"未审批":"已审批"},
                          student.status==0?{'id':student.auditId,'text':"拒绝",'onClick':this.reject}:{},
                          student.status==0?{'id':student.auditId,'text':"同意",'onClick':this.approve}:{}
                          ])
                      })
                      this.data.body = phone_data;
                  }
                  
    },
   
    async approve(item){

      const changephone = await this.$http.post(`user/changephone/approval?auditReply=1`,[item.id]);
       if(changephone.body){
                            ConfirmApi.show(this,{
                            title: '',
                            content: '操作成功',
                          });
                          this.search(this.condition);
                        }else{
                          ConfirmApi.show(this,{
                            title: '',
                            content: '操作失败',
                          });
       }
    },
    async reject(item){
        const changephone = await this.$http.post(`user/changephone/approval?auditReply=0`,[item.id]);
       if(changephone.body){
                            ConfirmApi.show(this,{
                            title: '',
                            content: '操作成功',
                          });
                           this.search(this.condition);
                        }else{
                          ConfirmApi.show(this,{
                            title: '',
                            content: '操作失败',
                          });
       }
    }
    
  },
  
};
</script>


