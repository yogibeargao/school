<template>
  <r-page>
      <top title="实习总结" :showBack="true"/>
      <r-body>
             <r-input title="分数:" :readonly="true"   :max="100" :min="0"  :model="this" value="score" :isNumber="true"/>
             <r-textarea title="评价:" :readonly="true"   :model="this" value="comments" :height="600" :max="600"></r-textarea>
       </r-body>
             <r-tab-bar v-if="!score">
                  <div class="example-simple">
    <div class="upload" >
      <ul>
        <li v-for="file in files" :key="file.id">
          <span>{{file.name}}</span> -
          <span>{{file.size | formatSize}}</span> -
          <span v-if="file.error">{{file.error}}</span>
          <span v-else-if="file.success">成功</span>
          <span v-else-if="file.active">active</span>
          <span v-else-if="file.active">active</span>
          <span v-else></span>
        </li>
      </ul>
      <div class="example-btn">
        <file-upload
          class="btn btn-primary"
          :multiple="false"
          :size="1024 * 1024 * 10"
          v-model="files"
          name="file" 
          :custom-action="customAction"
          @input-filter="inputFilter"
          @input-file="inputFile"
          ref="upload">
          <i class="fa fa-plus white"></i>
          实习报告
        </file-upload>
        <button type="button" class="btn btn-success" v-if="!$refs.upload || !$refs.upload.active" @click.prevent="$refs.upload.active = true">
          <i class="fa fa-arrow-up white" aria-hidden="true"></i>
          上传报告
        </button>
        <button type="button" class="btn btn-danger"  v-else @click.prevent="$refs.upload.active = false">
          <i class="fa fa-stop white" aria-hidden="true"></i>
          停止上传
        </button>
      </div>
    </div>
    
  </div>
              </r-tab-bar>
  </r-page>
</template>

<script>
import FileUpload from 'vue-upload-component'
export default {
  components: {
    FileUpload,
  },
  data() {
    return {
      files: [],
      score:null,
      comments:null,
      id:null
    };
  },
  methods: {
      async customAction(file, component){
       const self = this;
       const formData = new FormData();
      formData.append('file', file.file);
      if(this.id){
        formData.append('summaryId', this.id);
      }
      return await self.$http.post(`intern/summary/upload?filename=${file.name}`,formData);
    },
   inputFilter(newFile, oldFile, prevent) {
      if (newFile && !oldFile) {
        // Before adding a file
        // 添加文件前
        // Filter system files or hide files
        // 过滤系统文件 和隐藏文件
        if (/(\/|^)(Thumbs\.db|desktop\.ini|\..+)$/.test(newFile.name)) {
          return prevent()
        }
        // Filter php html js file
        // 过滤 php html js 文件
        if (/\.(php5?|html?|jsx?)$/i.test(newFile.name)) {
          return prevent()
        }
      }
    },
    inputFile(newFile, oldFile) {
      if (newFile && !oldFile) {
        // add
        console.log('add', newFile)
      }
      if (newFile && oldFile) {
        // update
        console.log('update', newFile)
      }
      if (!newFile && oldFile) {
        // remove
        console.log('remove', oldFile)
      }
    }
  },
  async mounted(){
                  const url = "intern/summary/stu";
                  const temp_record = await this.$http.get(url);
                  console.log(temp_record)
                  if(temp_record.body){
                    this.comments = temp_record.body.comments;
                    this.score = temp_record.body.score;
                    this.id = temp_record.body.id;
                  }
  },
  

};
</script>


<style>
.upload li{
  color: black;
}
.white{
  color: white;
}
.example-simple{
    text-align: center;
    width: 100%;
}
.example-simple label.btn {
  margin-bottom: 0;
  margin-right: 1rem;
}
.btn {
    display: inline-block;
    font-weight: 400;
    text-align: center;
    white-space: nowrap;
    vertical-align: middle;
    -webkit-user-select: none;
    -moz-user-select: none;
    -ms-user-select: none;
    user-select: none;
    border: 1px solid transparent;
    padding: .5rem .75rem;
    font-size: 1rem;
    line-height: 1.25;
    border-radius: .25rem;
    transition: all .15s ease-in-out;
}
.btn-primary {
    color: #fff;
    background-color: #007bff;
    border-color: #007bff;
}
.file-uploads {
    overflow: hidden;
    position: relative;
    text-align: center;
    display: inline-block;
}
.btn-success {
    color: #fff;
    background-color: #28a745;
    border-color: #28a745;
}
[type=reset], [type=submit], button, html [type=button] {
    -webkit-appearance: button;
}
button, select {
    text-transform: none;
}
button, input {
    overflow: visible;
}
</style>