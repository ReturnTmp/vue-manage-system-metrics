<template>
    <el-container class="body" style="height: 450px">
        <el-aside width="400px" style="position: relative">
      <el-tabs v-model="activeName" type="card">
        <el-tab-pane label="源代码上传" name="first">
          
          <el-upload
          class="upload-demo"
          drag
          :auto-upload="false"
          action="https://jsonplaceholder.typicode.com/posts/"
          :limit="1"
          :file-list="fileList"
          :on-change="onChange"
          :on-exceed="onExceed">
          <i class="el-icon-upload"></i>
          <div class="el-upload__text">将文件拖到此处，或<em>点击上传</em>
            <div class="el-upload__tip">请上传源代码的txt文件</div></div>
          </el-upload>
        </el-tab-pane>
        <el-tab-pane label="源代码输入" name="second">
          
          <el-input type="textarea" :rows="12" placeholder="请输入需要分析的代码" v-model="textarea">
      </el-input>
        </el-tab-pane>
      </el-tabs>
            <el-button class="submit" type="primary" round @click="uploadFile">上传文件</el-button>
        </el-aside>
        <el-main>
            <el-empty v-if="!tableData.length" description="请在左侧上传文件哦~"></el-empty>
            <el-table v-if="tableData.length" 
            :data="tableData"
            style="width: 100%">
            <el-table-column
              prop="codeLines"
              label="代码行数">
            </el-table-column>
            <el-table-column
              prop="blankLines"
              label="空白行">
            </el-table-column>
            <el-table-column
              prop="commentLines"
              label="注释行数">
            </el-table-column>
            <el-table-column
              prop="cycleComplexity"
              label="圈复杂度">
            </el-table-column>
          </el-table>
        </el-main>
    </el-container>
</template>

<script>
export default {
    name:'SourceCode',
    data() {
        return {
            fileList: [],
            tableData: [],
            activeName: 'first',
            textarea: ''
        }
    },
    methods: {
        onChange(file,fileList){
            console.log(file);
            console.log(fileList);
            this.fileList = fileList;
        },
        async uploadFile(){
          if(this.textarea.length!=0){
            console.log('上传输入');
            let res = await this.axios({
              url:'http://localhost:8080/txt/getRawCode',
              method:'POST',
              data: {code:this.textarea}
            })
            console.log('上传代码',res);
            this.tableData=[{...res.data.data}];
            return;
          }
          console.log('上传文件');
            let formData = new FormData();
                for (let i in this.fileList) {
                    formData.append('txt', this.fileList[i].raw);
                }
            let res = await this.axios({
              url:'http://localhost:8080/txt/uploadtxt',
              method: 'post',
              data: formData
            });
            console.log(res);
            let {data} = await this.axios({
                            url: 'http://localhost:8080/txt/getCycleComplexity',
                            method: 'get',
                        })
            this.tableData=[{...data.data}];
        },
        // 溢出则替换
        onExceed(files,fileList){
            this.fileList.pop();
            this.fileList.push(files[0]);
        },
    }
}
</script>

<style scoped>
.body {
    margin: 0 auto;
}
.submit {
    margin-top: 20px;
    margin-left: 130px;
}

</style>