<template>
  <!--导入word并识别内容-->
  <div class="upload-word">
    <el-upload
      class="upload-demo"
      action="/api/posts"
      :on-preview="handlePreview"
      :on-remove="handleRemove"
      :before-remove="beforeRemove"
      multiple
      :limit="3"
      :on-exceed="handleExceed"
      :file-list="fileList">
      <el-button size="small" type="primary">点击上传</el-button>
      <div slot="tip" class="el-upload__tip">只能上传jpg/png文件，且不超过500kb</div>
    </el-upload>
    <!--<el-input-->
      <!--type="textarea"-->
      <!--:rows="2"-->
      <!--placeholder="请输入内容"-->
      <!--:autosize="{ minRows:10, maxRows: 40}"-->
      <!--v-model="textarea">-->
    <!--</el-input>-->
    <!--<div class="upload-button">-->
      <!--<el-upload ref="uploadword" action="" :show-file-list="false" :auto-upload="false" :limit="1" accept="doc" style="margin-right: 10px;">-->
        <!--<el-button slot="trigger" icon="el-icon-upload" size="mini" class="f14s" type="primary">导入</el-button>-->
      <!--</el-upload>-->
      <!--&lt;!&ndash;<el-button  icon="el-icon-upload" size="mini" class="f14s" type="primary">编辑</el-button>&ndash;&gt;-->
    <!--</div>-->
    <div class="pdf" v-show="fileType === 'pdf'">
      <p class="arrow">
        <span @click="changePdfPage(0)" class="turn" :class="{grey: currentPage==1}">Preview</span>
        {{currentPage}} / {{pageCount}}
        <span @click="changePdfPage(1)" class="turn" :class="{grey: currentPage==pageCount}">Next</span>
      </p>
      <pdf
        :src="urls"
        :page="currentPage"
        @num-pages="pageCount=$event"
        @page-loaded="currentPage=$event"
        @loaded="loadPdfHandler">
      </pdf>
    </div>

    <pdf :src="urls" :page="currentPage" @loaded="loadPdfHandler" style="width: 100%;height: 100px;"></pdf>
  </div>
</template>

<script>
    import pdf from 'vue-pdf'
    export default {
        name: "index",
        components: {
          pdf
        },
        data(){
          return{
            // textarea: '',
            urls:'',
            currentPage: 1, // pdf文件页码
            pageCount: 2, // pdf文件总页数
            fileType: 'pdf', // 文件类型
            fileList: [{name: 'food.jpeg', url: 'https://fuss10.elemecdn.com/3/63/4e7f3a15429bfda99bce42a18cdd1jpeg.jpeg?imageMogr2/thumbnail/360x360/format/webp/quality/100'}, {name: 'food2.jpeg', url: 'https://fuss10.elemecdn.com/3/63/4e7f3a15429bfda99bce42a18cdd1jpeg.jpeg?imageMogr2/thumbnail/360x360/format/webp/quality/100'}]
          }
        },
        created() {
          // 有时PDF文件地址会出现跨域的情况,这里最好处理一下
          // this.urls = pdf.createLoadingTask(this.urls)
          this.urls=pdf.createLoadingTask("C:/Users/Administrator/Desktop/yz.pdf");
        },
        mounted(){
          this.$refs['uploadword'].$el.querySelector('.el-upload__input').addEventListener('change',e=>{this.readWord(e)})
        },
        methods:{
          // readWord(e){
          //   const files=e.target.files;
          //   const url=e.target.baseURI;
          //   this.urls=url;
          //   console.log(this.urls)
          // },
          changePdfPage (val) {
            if (val === 0 && this.currentPage > 1) {
              this.currentPage--
            }
            if (val === 1 && this.currentPage < this.pageCount) {
              this.currentPage++
            }
          },
          // pdf加载时
          loadPdfHandler (e) {
            this.currentPage = 1 // 加载的时候先加载第一页
          },
          handleRemove(file, fileList) {
            console.log(file, fileList);
          },
          handlePreview(file) {
            console.log(file);
          },
          handleExceed(files, fileList) {
            this.$message.warning(`当前限制选择 3 个文件，本次选择了 ${files.length} 个文件，共选择了 ${files.length + fileList.length} 个文件`);
          },
          beforeRemove(file, fileList) {
            return this.$confirm(`确定移除 ${ file.name }？`);
          }
        }
    }
</script>

<style scoped>
  .upload-button{
    display: flex;
  }
</style>
