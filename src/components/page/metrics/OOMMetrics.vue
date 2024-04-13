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
                    <div>点击选择要上传的文件（xml 文件）</div>
                </div>
            </el-upload>
            <el-button style='margin-top: 10px' class="submit" type="primary" @click="uploadFile">上传文件</el-button>
        </el-card>

        <el-card  v-if="!CKresList.length" class="box-card"style='width: 100%' >
            <el-empty  description="请先上传文件"></el-empty>
        </el-card>

        <el-card  class="box-card" v-if="CKresList.length" style='width: 100%' >
            <div style="height:500px;width: 100%;" ref="mainck"></div>
        </el-card>

        <el-card  class="box-card" v-if="CKresList.length" style='width: 100%' >
            <div style="height:500px;width: 100%;" ref="mainlk"></div>
        </el-card>

        <el-card class="box-card" style='width: 100%;display: flex'>
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
        </el-card>

    </el-container>
</template>

<script>
import * as echarts from 'echarts';
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
            tableData: [],
            UFCNumber: 218,
            stepNumber: 1,
            tableDataUFC: [
                {
                    date: "2016-05-02",
                    name: "王小虎",
                    address: "上海市普陀区金沙江路 1518 弄",
                    iseditor: false
                },
                {
                    date: "2016-05-04",
                    name: "王小虎",
                    address: "上海市普陀区金沙江路 1517 弄",
                    iseditor: false
                },
                {
                    date: "2016-05-01",
                    name: "王小虎",
                    address: "上海市普陀区金沙江路 1519 弄",
                    iseditor: false
                },
                {
                    date: "2016-05-03",
                    name: "王小虎",
                    address: "上海市普陀区金沙江路 1516 弄",
                    iseditor: false
                }
            ],
            metricsResult: 239.8
        }
    },
    mounted() { },
    methods: {
        drawLineCK() {
            const myChart = echarts.init(this.$refs.mainck);
            let legendData = this.CKresList.map(item => {
                return item.name
            })
            let seriesData = this.CKresList.map(item => {
                return {
                    value: [item.wmc, item.rfc, item.dit, item.noc, item.cbo],
                    name: item.name
                }
            })
            var option = {
                title: {
                    text: 'CK 度量'
                },
                legend: {
                    data: legendData
                },
                radar: {
                    // shape: 'circle',
                    indicator: [
                        { name: 'WMC', max: 10 },
                        { name: 'RFC', max: 10 },
                        { name: 'DIT', max: 10 },
                        { name: 'NOC', max: 10 },
                        { name: 'CBO', max: 10 },
                    ]
                },
                series: [
                    {
                        name: '',
                        type: 'radar',
                        data: seriesData
                    }
                ]
            };
            myChart.setOption(option)
        },
        drawLineLK() {
            const myChart = echarts.init(this.$refs.mainlk);
            let legendData = this.CKresList.map(item => {
                return item.name
            })
            let seriesData = this.CKresList.map(item => {
                return {
                    value: [item.wmc, item.rfc, item.dit, item.noc, item.cbo],
                    name: item.name
                }
            })
            var option = {
                title: {
                    text: 'LK 度量'
                },
                legend: {
                    data: legendData
                },
                radar: {
                    // shape: 'circle',
                    indicator: [
                        { name: 'CS', max: 10 },
                        { name: 'NOO', max: 10 },
                        { name: 'NOA', max: 10 },
                        { name: 'SI', max: 10 },
                    ]
                },
                series: [
                    {
                        name: '',
                        type: 'radar',
                        data: seriesData
                    }
                ]
            };
            myChart.setOption(option)
        },
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

            // 画雷达图
            this.drawLineCK()
            this.drawLineLK()
        },
        // 溢出则替换
        onExceed(files,fileList){
            this.fileList.pop();
            this.fileList.push(files[0]);
        },
        // 点击折叠面板出现的值
        handleChange(val){
            // console.log(val);
        },
        edit(row, index) {
            row.iseditor = true;
        },
        save(row, index) {
            row.iseditor = false;
        }
    },
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

</style>