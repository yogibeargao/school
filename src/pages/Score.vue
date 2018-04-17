<template>
  <page>
      <top title="实习成绩" :showBack="true"/>
      <r-body>
              <card>
                  <previewer title="总成绩" :value="score" :data="list1"></previewer>
              </card>
      </r-body>
  </page>
</template>

<script>
import { Page,Card,RBody} from "rainbow-mobile-core";
import {Previewer} from 'rainbow-mobile-previewer'
import Top from "../components/Top.vue";
import Util from "../util/util";

export default {
  components: {
    Top,
    Page,
    Card,
    Previewer,
    RBody
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
                const list = await this.$http.post(url,{"studentNos":[identityId],"pageNo":1,"pageSize":30});
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


