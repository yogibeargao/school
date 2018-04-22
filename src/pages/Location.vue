<template>
  <page>
      <top title="签到" :showBack="true"/>
      <r-body>
                    <card>
                          <r-table :data="data" />
                    </card>  
      </r-body>         
        <tab-bar>
            <cell type="row" :vertical="true">
                          <cell >
                            <box >
                                <r-button link="/location/detail" >在线签到</r-button>
                            </box>
                          </cell>
              </cell>
       </tab-bar>
  </page>
</template>

<script>
import { Page, RBody,RImage, RButton,TabBar, Cell, Box, MenuBar,Grid,Card,RTable } from "rainbow-mobile-core";
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
          [{'text':'时间'},{'text':'地点'}]
        ],
        "body":[]
      },
    };
  },
  computed :{
    
    
  },
  async mounted(){
                const identityId = Util.getIdentityId(this);
                const url = `online/signin/list`;
                const signList = await this.$http.post(url,{"identityId":identityId,"pageNo":1,"pageSize":50});
                if(signList.body){
                    this.data.body = _.map(signList.body,(s)=>{
                        return [{'text':s.signDate},{'text':s.signAddress}]
                    })
                }
                
  }
};
</script>


