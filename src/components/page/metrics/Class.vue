<template>
    <el-container style='display: block'>

        <h4>功能点度量页面</h4>
        <el-card class="box-card" style='width: 100%'>
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
                <div class="el-upload__text">将文件拖到此处，或<em>点击上传</em></div>
                <div class="el-upload__tip">请上传类图的xml文件</div>
            </el-upload>
            <el-button class="submit" type="primary" round @click="uploadFile">上传文件</el-button>


        </el-card>
        <el-card  class="box-card" style='width: 100%' >
            <el-empty v-if="!CKresList.length" description="请先上传文件"></el-empty>
            <el-tabs v-if="CKresList.length" v-model="activeName" tab-position="left" style="width: 800px; height: 400px">
                <el-tab-pane label="CK度量" name="first">
                    <el-collapse v-for="(item,index) in CKresList" :key="index" v-model="activeNames" accordion>
                        <el-collapse-item :title="item.name" :name="index">
                            <div>
                                <ul>
                                    <li>WMC:{{ item.wmc }}</li>
                                    <li>RFC:{{item.rfc}}</li>
                                    <li>DIT:{{item.dit}}</li>
                                    <li>NOC:{{item.noc}}</li>
                                    <li>CBO:{{item.cbo}}</li>
                                </ul>
                            </div>
                        </el-collapse-item>
                    </el-collapse>
                </el-tab-pane>
                <el-tab-pane label="LK度量" name="second">
                    <el-collapse v-for="(item,index) in LKresList" :key="index" v-model="activeNames" accordion>
                        <el-collapse-item :title="item.name" :name="index">
                            <div>
                                <ul>
                                    <li>CS:{{ item.cs }}</li>
                                    <li>NOO:{{item.noo}}</li>
                                    <li>NOA:{{item.noa}}</li>
                                    <li>SI:{{item.si}}</li>
                                </ul>
                            </div>
                        </el-collapse-item>
                    </el-collapse>
                </el-tab-pane>
                <el-tab-pane label="类图统计" name="third">
                    <el-table v-if="tableData.length"
                              :data="tableData"
                              style="width: 100%">
                        <el-table-column
                            prop="complex"
                            label="复杂度（WMC+DIT）">
                        </el-table-column>
                        <el-table-column
                            prop="inherit"
                            label="继承关系（DIT+NOC）">
                        </el-table-column>
                        <el-table-column
                            prop="couple"
                            label="耦合度（CBO+RFC）">
                        </el-table-column>
                    </el-table>

                </el-tab-pane>
            </el-tabs>

        </el-card>
    </el-container>
</template>

<script>
export default {
    name:'Class',
    data() {
        return {
            fileList: [],
            activeNames: ['1'],
            activeName: 'first',
            CKresList: [],
            LKresList: [],
            classList: [],
            tableData: []
        }
    },
    methods: {
        onChange(file,fileList){
            console.log(file);
            console.log(fileList);
            this.fileList = fileList;
        },
        async uploadFile(){
            let formData = new FormData();
                for (let i in this.fileList) {
                    formData.append('file', this.fileList[i].raw);
                }
            // 上传文件
            let res = await this.axios({
              url:'http://localhost:8080/xml/uploadxml',
              method: 'post',
              data: formData
            });
            console.log('上传文件',res);
            // 请求后台处理结果
            // 1.请求CK度量结果
            let CKData = await this.axios({
                url:'http://localhost:8080/xml/getCKvalue',
                method: 'get',
            })
            this.CKresList = CKData.data.data;
            console.log('CK',this.CKresList);
            // 2.请求LK度量结果
            let LKData = await this.axios({
                url:'http://localhost:8080/xml/getLKvalue'
            })
            this.LKresList = LKData.data.data;
            console.log('LK',this.LKresList);
            // 3.请求类详情信息
            // let {data} = await this.axios({
            //                 url: 'http://localhost:8080/xml/getBasicInfo',
            //                 method: 'get',
            //             })
            // this.classList = data.data;
            // console.log('classInfo',this.classList);
            // 3.进行类图统计
            // 遍历ck度量中的数据，求和
            let complex=0, inherit=0, couple=0;
            this.CKresList.forEach(item=>{
                complex+=(item.wmc+item.dit);
                inherit+=(item.dit+item.noc);
                couple+=(item.cbo+item.rfc);
            })
            this.tableData = [{
                complex: complex,
                inherit: inherit,
                couple: couple
            }]
        },
        // 溢出则替换
        onExceed(files,fileList){
            this.fileList.pop();
            this.fileList.push(files[0]);
        },
        // 点击折叠面板出现的值
        handleChange(val){
            // console.log(val);
        }
    }
}
</script>

<style scoped>
.body {
    margin: 0 auto;
}
.aside {
    position: relative;
}
.submit {
    margin-top: 20px;
    margin-left: 130px;
}

.box-card {
    margin-top: 20px;
}
.el-upload--text{
    display: none;
}

</style>