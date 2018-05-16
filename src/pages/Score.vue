<template>
  <r-page>
      <top title="实习成绩" :showBack="true"/>
      <r-body>
              <r-card>
                  <previewer title="总成绩" :value="score" :data="list1"></previewer>
              </r-card>
      </r-body>
  </r-page>
</template>

<script>
import {Previewer} from 'rainbow-mobile-previewer'
import Util from "../util/util";

export default {
  components: {
    Previewer,
  },
  data() {
    return {
      score:"0",
      list1: []
    };
  },
  methods: {
    onChange() {}
  },
  async mounted(){
                const identityId = Util.getIdentityId(this);
                const url = `intern/score/list`;
                const list = await this.$http.post(url,{"studentNos":[identityId],"pageNo":1,"pageSize":50});
                if(list.body){
                  this.score = list.body[0].schoolScore?list.body[0].schoolScore+"":"还没出成绩";
                  this.list1 = [{
                        label: '学生姓名',
                        value: list.body[0].studentName
                      }, {
                        label: '老师姓名',
                        value: list.body[0].teacherName
                  }]
                }
                
  }
};
</script>


