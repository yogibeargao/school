<template>
  <page>
      <top title="记录详情" :showBack="true"/>
      <r-body>
              <card>
                  <date-time :readonly="isreadonly"  title='开始时间' format="YYYY-MM-DD HH:mm" :model="this.record" value="startDateStr" :minuteList="['00', '15', '30', '45']"></date-time>
                  <date-time  :readonly="isreadonly" title='结束时间' format="YYYY-MM-DD HH:mm" :model="this.record" value="endDateStr"  :minuteList="['00', '15', '30', '45']"></date-time>
              </card>
              <card>
                  <r-textarea title='实习描述:' :readonly="isreadonly" placeholder="请在这里输入实习描述" :model="this.record" value="internDescription" :height="200" :max="200"></r-textarea>
              </card>
                <card>
                  <r-textarea title='实习评价:' :readonly="isreadonly" placeholder="请在这里输入实习评价" :model="this.record" value="apprisal"  :autoSize="true" :rows="10" :max="200"></r-textarea>
              </card>
      </r-body>
                            <toast :model="this" value="showFlag" :text="toastText" :type='type'/>

              <tab-bar v-if="isShow&&isStudent">
                <cell type="row" :vertical="true" >
                              <cell >
                                  <box >
                                      <r-button :onClick="submit">提交</r-button>
                                  </box>
                              </cell>
                  </cell>
             </tab-bar>
  </page>
</template>

<script>
import { Page, RBody,RImage,RTextarea,Toast, RButton, Selector,Cell, Box,TabBar, DateTime,Grid,Card,RTable,Selecter} from "rainbow-mobile-core";
import  Top from '../components/Top.vue';
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
    Toast
  },
  data() {
    return {
      record:{},
      isShow:false,
      toastText:"操作失败",
      type : "warn",
      showFlag:false,
      apprisal:false
    };
  },
  methods :{
    async submit(){
                  const url = "intern/detail/create";
                  const identityId = Util.getIdentityId(this);
                  this.record["studentNo"]= identityId;
                  this.record.startDateStr = this.record.startDateStr+":00";
                  this.record.endDateStr = this.record.endDateStr+":00";
                  const temp_record = await this.$http.post(url,this.record);
                  if(temp_record.body){
                      this.toastText="操作成功";
                      this.type = "success";
                      this.showFlag=true;
                  }else{
                      this.showFlag=true;
                  }
    }
  },
  computed:{
      isStudent(){
      return Util.isStudent(this);
      },
      isreadonly(){
        
        return !(this.isShow&&this.isStudent);
      }
  },
   async mounted(){
          const id = this.$route.query.id;
          if(id){
                  const url = "intern/detail?internId="+id;
                  const temp_record = await this.$http.get(url);
                  if(temp_record.body){
                    temp_record.body.startDateStr = temp_record.body.startDateStr?temp_record.body.startDateStr.substring(0,16):"";
                    temp_record.body.endDateStr = temp_record.body.endDateStr?temp_record.body.endDateStr.substring(0,16):"";
                    this.record = temp_record.body;
                    this.apprisal = temp_record.body.apprisal;
                  }
             
                  
          }
  },
  async created(){
                  const id = this.$route.query.id;
                  if(id){
                      const auditUrl = "user/processaudit?processCode=interndetail";
                      const audit = await this.$http.get(auditUrl);
                      this.isShow = audit.body;
                  }else if(this.isStudent){
                      this.isShow = true;
                  }
                  
  }
};
</script>


