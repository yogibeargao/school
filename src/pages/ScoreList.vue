<template>
  <page>
      <top title="学生成绩" :showBack="true"/>
      <r-body>
              <card>
                  <selector  title="班级" :options="classes" :model="this" value="class" :onChange="classChange"></selector>
                  <row title="学生" :model="this" value="v_student"  :onClick="showStudent" :isLink="true" ></row>

              </card>
           
              
              <card>
                   <r-table :data="data" />
              </card>

             
      </r-body>
           <r-dialog :model="this" value="showDialog" :disableMask="false">
                        <checker :model="this" value="student" :data='students' type="list" :onChange="onChange"/>
             </r-dialog>
  </page>
</template>

<script>
import {
  Page,
  RImage,
  RButton,
  RTextarea,
  Picker,
  Cell,
  Box,
  TabBar,
  DateTime,
  Grid,
  Card,
  RTable,
  RDialog,
  RBody,
  Selector,
  Checker,
  Row
} from "rainbow-mobile-core";
import Top from "../components/Top.vue";
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
    Picker,
    RTextarea,
    TabBar,
    Cell,
    RBody,
    Selector,
    RDialog,
    Checker,
    Row
  },
  data() {
    return {
      showDialog: false,
      students: [],
      v_student: null,
      class: null,
      classes:[],
      student:null,
      data:{
        "head":[
          [{'text':'姓名'},{'text':'分数'},{'text':'操作'}]
        ],
        "body":[]
      },

    };
  },
  methods: {
     async onChange(){
                  const v_student_List = [];
                  const student_Nos = [];
                  _.each(this.students,(cla)=>{
                      _.each(this.student,(pcla)=>{
                            if(pcla==cla.key){
                                  v_student_List.push(cla.value);
                                  student_Nos.push(cla.key);
                            }
                      });
                  });
                  this.v_student = _.isEmpty(v_student_List)?"":v_student_List.join(",");

                  const param = {"classId":this.class,"studentNos":student_Nos,"pageNo":1,"pageSize":30};
                  const scores = await this.$http.post(`intern/score/list`,param);
                  const scores_data = [];
                  _.each(v_student_List,(student,index)=>{
                      scores_data.push([{'text':student},{'text':''},{'text':'打分','link':"/score/detail?id="+student_Nos[index]}])
                  })
                  this.data.body = scores_data;
                  sessionStorage.setItem("class",this.class);
                  sessionStorage.setItem("student_Nos",JSON.stringify(student_Nos));
                  sessionStorage.setItem("scores_data",JSON.stringify(scores_data));

    },
    showStudent(){
        this.showDialog=true;
    },
    async classChange(){
                  const identityId = Util.getIdentityId(this);
                  const students = await this.$http.get(`user/class/students?identityId=${identityId}&&classNo=${this.class}`);
                  const _students = [];
                  _.each(students.body,(stu)=>{
                      _students.push({"key":stu.studentNo,"value":stu.studentName})
                  });
                  this.students=_students;
    }
  },
   async mounted(){
                  const identityId = Util.getIdentityId(this);
                  const classes = await this.$http.get(`user/teacherclass?identityId=${identityId}`);
                  this.classes = _.map(classes.body,(cla)=>{
                      return {"key":cla.classNo,"value":cla.className}
                  })
                  const _class = sessionStorage.getItem("class");
                  const _student_Nos = sessionStorage.getItem("student_Nos");

                  if(_class&&_student_Nos){
                          const param = {"classId":_class,"studentNos":JSON.parse(_student_Nos),"pageNo":1,"pageSize":30};
                          const scores = await this.$http.post(`intern/score/list`,param);
                          console.log(scores)
                          const _scores_data = sessionStorage.getItem("scores_data");
                          this.data.body = JSON.parse(_scores_data);
                  }
  }
};
</script>


