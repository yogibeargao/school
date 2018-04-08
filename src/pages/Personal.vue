<template>
  <page>
      <top title="个人信息" :showBack="true"/>
      <r-body>
            <r-image :list="userImg"/>
            <row title="姓名" :model="this.user" value='studentName' v-if="isStudent"/>
            <row title="姓名" :model="this.user" value='teachername' v-if="!isStudent"/>
            <row title="性别" :model="this.user" value='sex'/>
            <row title="学号" :model="this.user" value='studentNo' v-if="isStudent"/>
            <row title="工号" :model="this.user" value='teacherNo' v-if="!isStudent"/>
            <row title="实习单位" :model="this.user" value='internShipUnit' v-if="isStudent"/>
            <row title="实习地址" :model="this.user" value='workAddress' v-if="isStudent"/>
            <row title="工作地址" :model="this.user" value='address' v-if="!isStudent"/>
            <row title="电话" :model="this.user" value='phoneNo' :isLink="isStudent" :onClick="changPhone"/>
            <confirm :model="this" value="show" :showInput="true" inputType="number" title="修改电话" :onConfirm="modifyPhone"/>
      </r-body>     
  </page>
</template>

<script>
import { Page, RBody,RImage, RButton,TabBar,Confirm,RForm, Row, Box, MenuBar,Grid,Card,RTable } from "rainbow-mobile-core";
import  Top from '../components/Top.vue';
import user from "../assets/user.gif";
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
    Row,
    RImage,
    RBody,
    Confirm
  },
  data() {
    return {
       userImg: [
        {
          src: user
        }
      ],
      user:{},
      isStudent:true,
      show:false
    };
  },
  methods :{
    changPhone(){
        this.show=true;
    }, 
    modifyPhone(value){
       
    }
  },
  async mounted(){
                this.user = JSON.parse(sessionStorage.getItem("user"));
                const url = "user/detail?identityId="+this.user.identityId;
                const signList = await this.$http.get(url);
                this.isStudent = Util.isStudent(this);
                if(this.isStudent){
                    this.user = signList.body.student;
                }else{
                    this.user = signList.body.teacher;
                }
  }
};
</script>


