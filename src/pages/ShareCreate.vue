<template>
  <page>
      <top title="位置共享" :showBack="true"/>
      <r-body>
              <search :condition="condition" :callBack="search"  :showTime="false"/>
      </r-body>  
                    <confirm :model="this" value="show" title="您确认发起共享么?"  :onConfirm="onConfirm">共享学生是：{{ condition.v_student }}</confirm>
   
        <tab-bar>
                      <toast :model="this" value="showFlag" :text="toastText" :type='type'/>

            <cell type="row" :vertical="true">
                          <cell >
                            <box >
                                <r-button :onClick="shareCreate" :disabled="disableButton">发起响应</r-button>
                            </box>
                          </cell>
              </cell>
       </tab-bar>
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
          [{'text':'时间'},{'text':'地点'}]
        ],
        "body":[]
      },
      classes:[],
      options2:[['张三','李四']],
      startDate:null,
      name:[],
      students:[],
      condition:{},
      share:{},
      show:false,
      showFlag:false,
      toastText:"",
      type:"success",
      showDialog:false
    };
  },
  computed :{
      disableButton(){
        return _.isEmpty(this.condition.student);
      }
    
  },
  methods:{
    shareCreate(){
        this.show=true;
    },
    async onConfirm(){
       const status = await this.$http.post(`location/sharing/create`,{"studentNos":this.condition.student});
      if(status){
        this.toastText="发起成功";
        this.showFlag=true;
      }else{
        this.toastText="发起失败";
        this.type = "warn";
        this.showFlag=true;
      }
    },
    search(){
        this.showDialog=true;
    },
  }
 
};
</script>


