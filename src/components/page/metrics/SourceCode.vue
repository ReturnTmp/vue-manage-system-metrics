<template>
    <el-container style='display: block'>
        <el-card class="box-card" style='line-height: 30px'>
            <h3>功能点度量页面</h3>
            <p>在本页面设置各类功能点数量和系统特征，将根据功能点度量的方法得出度量结果</p>
        </el-card>

        <el-card class="box-card" style='width: 100%;display: flex'>

            <el-upload
                class="upload-demo"
                :auto-upload="false"
                action="https://jsonplaceholder.typicode.com/posts/"
                :limit="1"
                :file-list="fileList"
                :on-change="onChange"
                :on-exceed="onExceed">
                <div style='display: flex'>
                    <i class="el-icon-document-checked" style='font-size: 20px;margin-top: 10px;margin-left: 20px;margin-right: 10px'></i>
                    <div>点击选择要上传的文件（txt 文件）</div>
                </div>
            </el-upload>
            <el-button style='margin-top: 10px' class="submit" type="primary" @click="uploadFile">上传文件</el-button>
        </el-card>

        <el-card class='box-card'>

            <el-empty v-if="!tableData.length" description="请先上传文件"></el-empty>

            <el-row v-if='tableData.length'>
                <el-col :span="4" class='col-box'>
                    <el-card :body-style="{ padding: '0px' }">
                        <div style="padding: 14px;">
                            <i class="el-icon-s-data" style='font-size: 20px;margin-top: 10px;margin-left: 20px;margin-right: 10px'></i>

                            <span style='font-size: 20px;font-weight: bold'>代码行数</span>
                            <div class="bottom clearfix">
                                {{tableData[0].codeLines}}
                            </div>
                        </div>
                    </el-card>
                </el-col>
                <el-col :span="4" class='col-box'>
                    <el-card :body-style="{ padding: '0px' }">
                        <div style="padding: 14px;">
                            <i class="el-icon-s-marketing" style='font-size: 20px;margin-top: 10px;margin-left: 20px;margin-right: 10px'></i>

                            <span style='font-size: 20px;font-weight: bold'>空白行数</span>
                            <div class="bottom clearfix">
                                {{tableData[0].blankLines}}
                            </div>
                        </div>
                    </el-card>
                </el-col>
                <el-col :span="4" class='col-box'>
                    <el-card :body-style="{ padding: '0px' }">
                        <div style="padding: 14px;">
                            <i class="el-icon-s-flag" style='font-size: 20px;margin-top: 10px;margin-left: 20px;margin-right: 10px'></i>

                            <span style='font-size: 20px;font-weight: bold'>注释行数</span>
                            <div class="bottom clearfix">
                                {{tableData[0].commentLines}}
                            </div>
                        </div>
                    </el-card>
                </el-col>
                <el-col :span="4" class='col-box'>
                    <el-card :body-style="{ padding: '0px' }">
                        <div style="padding: 14px;">
                            <i class="el-icon-s-opportunity" style='font-size: 20px;margin-top: 10px;margin-left: 20px;margin-right: 10px'></i>

                            <span style='font-size: 20px;font-weight: bold'>圈复杂度</span>
                            <div class="bottom clearfix">
                                {{tableData[0].cycleComplexity}}
                            </div>
                        </div>
                    </el-card>
                </el-col>
            </el-row>


        </el-card>
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

.box-card {
    margin-top: 20px;
}

/deep/ .el-upload{
    width: 320px;
    height: 40px;
    line-height: 40px;
}
.col-box{
    margin-left: 50px;
    margin-right: 50px;
    width: 17%;
}

.bottom {
    margin-top: 13px;
    line-height: 12px;
    font-size: 25px;
    font-weight: bold;
    text-align: center;
}


.clearfix:before,
.clearfix:after {
    display: table;
    content: "";
}

.clearfix:after {
    clear: both
}

</style>