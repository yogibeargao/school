<template>
  <page>
      <top title="请假详情" :showBack="true"/>
      <r-body>
              <card>
                  <date-time  title='开始时间' :required="true" :readonly="!isShowDetail" :model="this" value="leaveStartDate" format="YYYY-MM-DD HH:mm" :hourList="['09', '10', '11', '12', '13', '14', '15', '16', '17', '18']" :minuteList="['00', '15', '30', '45']"></date-time>
                  <date-time  title='结束时间' :required="true" :readonly="!isShowDetail" :model="this" value="leaveEndDate" format="YYYY-MM-DD HH:mm" :hourList="['09', '10', '11', '12', '13', '14', '15', '16', '17', '18']" :minuteList="['00', '15', '30', '45']"></date-time>
              </card>
              <card>
                  <selector  title="请假类型" :required="true" :readonly="!isShowDetail" :options="options" :model="this" value="leaveType" :onChange="type"></selector>
              </card>
           
              <card>
                  <r-textarea placeholder="请假事由" :required="true" :readonly="!isShowDetail" :model="this"  value="reason" :height="200" :max="300"></r-textarea>
              </card>

              <card title="上传病假单" v-if="isShowDetail&&isShowUpload">
                  <upload :max="1" url="leave/img" name="file" :onSuccess="uploadSuccess" />
              </card>

                   <cell type="row" :vertical="true">
                                <cell>
                                  <box>
                                      <r-button v-if="state==0&&!isStudent" :onClick="approve" >审核通过</r-button>
                                      <r-button v-if="state==0&&!isStudent" :onClick="reject" type="danger">审核拒绝</r-button>
                                      <r-button v-if="state==0&&!isStudent" :onClick="download" >下载病假单</r-button>

                                  </box>
                                </cell>
                    </cell>
      </r-body>
             <tab-bar v-if="isShowDetail">
                  <cell type="row" :vertical="true">
                                <cell>
                                  <box>
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
} from "rainbow-mobile-core";
import {Upload} from "rainbow-mobile-upload";
import Top from "../components/Top.vue";
import Util from "../util/util";
import Vue from 'vue';

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
    Upload,
    RBody,
    RImage
  },
  data() {
    return {
      isShow:false,
      state:null,
      leavePath:null,
      leaveStartDate:null,
      leaveEndDate:null,
      leaveType:null,
      reason:null,
      leaveImg:null,
      options: [{ key: 2, value: "事假" }, { key: 1, value: "病假" }],
      showBill:false
    };
  },

  methods: {
    type(){
      if(this.leaveType==1){
        this.showBill = true;
      }
    },
    download(){
        const id = this.$route.query.leaveId;
        window.open(Vue.http.options.root+"/leave/download?leaveId="+id);
    },
    async submit() {
            if(!this.leavePath&&this.leaveType==1){
                  ConfirmApi.show(this,{
                    title: '',
                    content: '请上传病假单',
                  });
            }else{
                const url = `leave/create`;
                let param = {"imgUrl":this.leavePath,"reason":this.reason,"leaveStartDateStr":this.leaveStartDate+":00","leaveEndDateStr":this.leaveEndDate+":00","leaveType":this.leaveType};
                const leaves = await this.$http.post(url,param);
                if(!_.isEmpty(leaves.body)){
                  ConfirmApi.show(this,{
                    title: '申请失败',
                    content: leaves.bodyText,
                  });
                }else{
                    this.$router.back()
                }
            }
         
    },
    async approve() {
                const leaveId = this.$route.query.leaveId;
                const url = `leave/approval?auditReply=1`;
                const leaves = await this.$http.post(url,[Number(leaveId)]);
                this.$router.back()
    },
     async reject() {
                const leaveId = this.$route.query.leaveId;
                const url = `leave/approval?auditReply=0`;
                const leaves = await this.$http.post(url,[Number(leaveId)]);
                this.$router.back()
    },
    uploadSuccess(data){
        if(data){
          this.leavePath=data;
        }
    }
  },
  computed:{
    isReadlony(){
      return this.$route.query.leaveId?true:false;
    },
    isStudent(){
      return Util.isStudent(this);
    },
    isShowUpload(){
            const leaveId = this.$route.query.leaveId;
      return this.leaveType==1&&!leaveId?true:false;
    },
    isShowDetail(){
      const leaveId = this.$route.query.leaveId;
      if(leaveId){
          return false;
      }else{
          const type = this.$route.query.type;
          if(type=='submit'){
              return true;
          }else if(this.leaveType==1&&Util.isStudent(this)){
              return true;
          }else{
              return false;
          }
      }
    }
  },
  async mounted(){
           const leaveId = this.$route.query.leaveId;
           if(leaveId){
                  const leave = await this.$http.get("leave/detail?leaveId="+leaveId);
                  this.state=leave.body.state;
                  this.leaveType = leave.body.leaveType;
                  this.reason = leave.body.reason;
                  this.leaveEndDate = leave.body.leaveEndDate;
                  this.leaveStartDate = leave.body.leaveStartDate;
                  this.leaveImg = [{"class":"index","src":leave.body.imgUrl}];
                 
           }else{
                this.isShow = true;
           }
   
  },
  async created(){
                  const url = "user/processaudit?processCode=leave";
                  const temp_record = await this.$http.get(url);
                  this.isShow = temp_record.body;
  }
};
</script>
<style>
.cover{
  display: none
}
</style>


