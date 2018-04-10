<template>
  <page>
      <top title="实习总结" :showBack="true"/>
      <r-body>


                  <r-textarea placeholder="请输入实习总结描述" :model="this" value="name" :height="600" :max="600"></r-textarea>
      </r-body>
             <tab-bar>
                  <cell type="row" :vertical="true">
                                <cell >
                                  <box >
                                      <r-button >提交</r-button>
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
  RBody
} from "rainbow-mobile-core";



import Top from "../components/Top.vue";
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
    RBody
  },
  data() {
     return {
      url: 'http://your-post.url',
      headers: {'access-token': '<your-token>'},
      filesUploaded: []
    };
  },
  methods: {
     thumbUrl (file) {
      return file.myThumbUrlProperty
    },
    onFileChange (file) {
      // Handle files like:
      this.fileUploaded = file
    }
  }
};
</script>


