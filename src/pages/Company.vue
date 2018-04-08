<template>
  <page>
      <top title="实习单位" :showBack="true"/>
      <r-body>
       <card>
             <r-table :data="data" />
       </card>
      </r-body>
       <tab-bar>
            <cell type="row" :vertical="true">
                        <cell>
                            <box>
                                <r-button :onClick="addCompany">增加</r-button>
                            </box>
                        </cell>
            </cell>
       </tab-bar>
  </page>
</template>

<script>
import { Page, RImage,RBody, RButton,TabBar, Cell, Box, MenuBar,Grid,Card,RTable } from "rainbow-mobile-core";
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
    TabBar,
    Cell,
    RBody
  },
  data() {
    return {
       data:{
         "head":[
          [{'text':'单位名称'},{'text':'单位地址'},{'text':'当前单位'}]
        ],
        "body":[]
      },
    };
  },
  methods :{
      addCompany(){
          this.$router.push({"name":"CompanyDetail"});
      }
  },
  async mounted(){
                const identityId = Util.getIdentityId(this);
                const url = `intern/company/stulist?studentNo=`+identityId;
                const list = await this.$http.get(url);
                console.log(list)
                if(list.body){
                    this.data.body = _.map(list.body,(s)=>{
                        return [{'text':s.companyName,'link':'/company/detail?id='+s.internId},{'text':s.companyAddress},{'text':s.currentCompany==1?"是":"否"}]
                    })
                }
                
  }
};
</script>


