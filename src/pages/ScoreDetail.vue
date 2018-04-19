<template>
  <page>
      <top title="实习评价" :showBack="true"/>
      <r-body>
            <r-input title="分数" :required="true" :max="100" :min="0"  :model="this" value="schoolScore" :isNumber="true"/>
             <r-textarea placeholder="请输入评价" :model="this" value="comments" :height="600" :max="600"></r-textarea>
      </r-body>
             <tab-bar v-if="!schoolScore">
                  <cell type="row" :vertical="true">
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
import {
  Page,
  RImage,
  RButton,
  RTextarea,
  Selector,
  Cell,
  Box,
  TabBar,
  DateTime,
  Grid,
  Card,
  RTable,
  Selecter,
  RBody,
  RInput
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
    Selector,
    RTextarea,
    TabBar,
    Cell,
    RBody,
    RInput
  },
  data() {
    return {
      schoolScore: null,
      comments: null,
    };
  },
  methods: {
    async submit() {
                  const s_identityId =   this.$route.query.id;
                  const identityId = Util.getIdentityId(this);
                  const result = await this.$http.post(`intern/score/create`,{"studentNo":s_identityId,"teacherNo":identityId,"schoolScore":Number(this.score),"comments":this.comments});
                  if(result.body){
                     this.$router.back(-1);
                  }
    }
  },
  async mounted(){
          const id = this.$route.query.id;
          if(id){
                  const url = "intern/score/detail?studentSchoolScoreId="+id;
                  const temp_record = await this.$http.get(url);
                  if(temp_record.body){
                    this.comments = temp_record.body.comments;
                    this.schoolScore = temp_record.body.schoolScore;
                  }
          }
  
  }
};
</script>

