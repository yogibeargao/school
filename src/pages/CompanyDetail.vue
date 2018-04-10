<template>
  <page>
      <top title="实习单位详情" :showBack="true"/>
      <r-body>
                <card>
                    <r-input title="名称" :required="true" :model="this.company" value="companyName" placeholder="请输入实习单位名称"/>
                    <r-input title="地址" :required="true" :model="this.company" value="companyAddress" placeholder="请输入实习单位地址"/>
                </card>
                <card>
                    <date-time  title='开始时间' :required="true" :model="this.company" value="internStartDateStr" format="YYYY-MM-DD HH:mm" :hourList="['09', '10', '11', '12', '13', '14', '15', '16', '17', '18']" :minuteList="['00', '15', '30', '45']"></date-time>
                    <date-time  title='结束时间' :required="true" :model="this.company" value="internEndDateStr" format="YYYY-MM-DD HH:mm" :hourList="['09', '10', '11', '12', '13', '14', '15', '16', '17', '18']" :minuteList="['00', '15', '30', '45']"></date-time>
                </card>
                <card>
                    <r-input title="导师名字" :required="true" :model="this.company" value="tutor" placeholder="请输入导师名字"/>
                    <r-input title="导师电话" :required="true" :model="this.company" value="tutorPhoneNo" placeholder="请输入导师手机号"/>
                    <date-time  title='工作时间' :required="true" :model="this.company" value="workingTime" format="HH:mm" :hourList="['09', '10', '11', '12', '13', '14', '15', '16', '17', '18']" :minuteList="['00', '15', '30', '45']"></date-time>
                </card>
                 <card>
                    <r-switch title="是否当前单位" :required="true" :model="this.company" value="currentCompany" />
                </card>
                
      </r-body>
                <tab-bar>
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
import { Page, RImage,RBody, RInput,RButton,RSwitch, Selector,Cell, Box,TabBar, DateTime,Grid,Card,RTable,Selecter} from "rainbow-mobile-core";
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
    RInput,
    TabBar,
    Cell,
    RBody,
    RSwitch
  },
  data() {
    return {
      company:{},
    };
  },
  methods :{
    async submit(){
                const id = this.$route.query.id;
                const identityId = Util.getIdentityId(this);
                let url = `intern/company/create`;
                if(id){
                   url = `intern/company/update`
                }
                    this.company.internStartDateStr = this.company.internStartDateStr+":00";
                    this.company.internEndDateStr = this.company.internEndDateStr+":00";
                    this.company.workingTime = this.company.workingTime+":00";;
                    this.company.currentCompany = this.company.currentCompany?1:0;
                    this.company["studentNo"]=identityId;
                    const list = await this.$http.post(url,this.company);
                    this.$router.back(-1);
    }
  },
  async mounted(){
                    const id = this.$route.query.id;
                    if(id){
                      const url = `intern/company/detail?companyId=`+id;  
                      const list = await this.$http.get(url);
                      if(list.body){
                          list.body.internStartDateStr = list.body.internStartDate?list.body.internStartDate.substring(0,16):"";
                          list.body.internEndDateStr = list.body.internEndDate?list.body.internEndDate.substring(0,16):"";
                          list.body.workingTime = list.body.workingTime?list.body.workingTime.substring(0,16):"";
                          list.body.currentCompany = list.body.currentCompany==1?true:false;
                          
                          this.company = list.body;
                          console.log(this.company)
                          delete this.company["internStartDate"];
                          delete this.company["internEndDate"];
                      }
                   }
                
  }
};
</script>


