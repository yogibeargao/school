<template>
  <page>
      <top title="手机审批" :showBack="true"/>
      <r-body>
                   <card>
                  <selector title="班级" :options="classes" :model="this.phone" value="classes" :onChange="classChange" ></selector>
                  <row title="学生" :model="this" value="v_student"  :onClick="showStudent" :isLink="true" ></row>
              </card>
                  <card>
                      <r-table :data="data" />
                  </card>  

      </r-body>         
   <r-dialog :model="this" value="showDialog" :disableMask="false">
                                                <checker :model="this.phone" value="student" :data='students' type="list" :onChange="onChange"/>
       </r-dialog>
     
  </page>
</template>

<script>
import { Page, RBody,RImage, RButton,TabBar,Picker, Cell, Box, DateTime,Grid,Card,RTable,Selector,Checker,Row,RDialog,ConfirmApi } from "rainbow-mobile-core";
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
    Picker,
    DateTime,
    RBody,
    Selector,
    Checker,
    Row,
    RDialog,
    ConfirmApi
  },
  data() {
    return {
        data:{
        "head":[
          [{'text':'姓名'},{'text':'状态'},{'text':'操作'},{'text':'操作'}]
        ],
        "body":[]
      },
      classes:[],
      options2:[['张三','李四']],
      startDate:null,
      name:[],
      students:[],
      v_student:[],
      phone:{},
      show:false,
      showFlag:false,
      toastText:"",
      type:"success",
      showDialog:false
    };
  },
  methods :{
     shareCreate(){
        this.show=true;
    },
    async onConfirm(){
       const status = await this.$http.post(`location/sharing/create`,{"studentNos":this.share.student});
      if(status){
        this.toastText="发起成功";
        this.showFlag=true;
      }else{
        this.toastText="发起失败";
        this.type = "warn";
        this.showFlag=true;
      }
    },
    showStudent(){
        this.showDialog=true;
    },
    async onChange(){
                  const v_student_List = [];
                  const student_Nos = [];
                  _.each(this.students,(cla)=>{
                      _.each(this.phone.student,(pcla)=>{
                            if(pcla==cla.key){
                                  v_student_List.push(cla.value);
                                  student_Nos.push(cla.key);
                            }
                      });
                  });
                  this.v_student = _.isEmpty(v_student_List)?"":v_student_List.join(",");

                  const param = {"studentNos":student_Nos,"pageNo":1,"pageSize":30};
                  const phones = await this.$http.post(`user/changephone/list`,param);
                  if(_.size(phones.body)>0){
                      const phone_data = [];
                      _.each(v_student_List,(student,index)=>{
                          const _student = _.find(student_Nos,(s)=>{
                              return s==phones.body[index].studentNo
                          });
                          if(_student){
                          phone_data.push([{'text':student},{'text':phones.body[index].status==0?"未审批":"已审批"},
                          phones.body[index].status==0?{'id':phones.body[index],'text':"拒绝",'onClick':this.reject}:{},
                          phones.body[index].status==0?{'id':phones.body[index],'text':"同意",'onClick':this.approve}:{}
                          ])
                          }
                      })
                      this.data.body = phone_data;
                  }
                  


    },
    async classChange(){
                  const identityId = Util.getIdentityId(this);
                  const students = await this.$http.get(`user/class/students?identityId=${identityId}&&classNo=${this.phone.classes}`);
                  const _students = [];
                  _.each(students.body,(stu)=>{
                      _students.push({"key":stu.studentNo,"value":stu.studentName})
                  });
                  this.students=_students;
    },
    async approve(item){

      const changephone = await this.$http.post(`user/changephone/approval?auditReply=1`,[item.id.auditId]);
       if(changephone.body){
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
    },
    async reject(item){
        const changephone = await this.$http.post(`user/changephone/approval?auditReply=0`,[item.id.auditId]);
       if(changephone.body){
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
   async mounted(){
                  const identityId = Util.getIdentityId(this);
                  const classes = await this.$http.get(`user/teacherclass?identityId=${identityId}`);
                  this.classes = _.map(classes.body,(cla)=>{
                      return {"key":cla.classNo,"value":cla.className}
                  })
  }
};
</script>


