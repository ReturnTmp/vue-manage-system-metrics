<template>
    <el-container style='display: block'>

        <el-card class="box-card" style='line-height: 30px'>
            <h3>面向对象度量页面</h3>
            <p>在本页面中上传类图，我们将根据面向对象度量的方法得出度量结果，并为您提供优化面向对象设计的建议</p>
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
                    <div>点击选择要上传源代码（txt 文件）</div>
                </div>
            </el-upload>
            <el-button style='margin-top: 10px' class="submit" type="primary" @click="uploadFile">上传文件</el-button>
        </el-card>


        <el-card  v-if="!CKresList.length" class="box-card"style='width: 100%' >
            <el-empty  description="请先上传文件"></el-empty>
        </el-card>

        <el-card  class="box-card" v-if="CKresList.length" style='width: 100%' >
            <!--            <div style="height:500px;width: 100%;" ref="mainck"></div>-->
            <div style="height:500px;width: 100%;" ref="mainChart"></div>
        </el-card>

        <!--        <el-card  class="box-card" v-if="LKresList.length" style='width: 100%' >-->
        <!--            <div style="height:500px;width: 100%;" ref="mainlk"></div>-->
        <!--        </el-card>-->

        <el-card class="box-card" style='width: 100%;display: flex'>
            <el-table v-if="CKAndLKList.length"
                      :data="CKAndLKList"
                      style="width: 100%">
                <el-table-column
                    prop="name"
                    label="类名">
                </el-table-column>
                <el-table-column
                    prop="wmc"
                    label="WMC">
                </el-table-column>
                <el-table-column
                    prop="rfc"
                    label="RFC">
                </el-table-column>
                <el-table-column
                    prop="lcom"
                    label="LCOM">
                </el-table-column>
                <el-table-column
                    prop="cbo"
                    label="CBO">
                </el-table-column>
                <el-table-column
                    prop="dit"
                    label="DIT">
                </el-table-column>
                <el-table-column
                    prop="noc"
                    label="NOC">
                </el-table-column>

                <el-table-column
                    prop="cs"
                    label="CS">
                </el-table-column>
                <el-table-column
                    prop="noo"
                    label="NOO">
                </el-table-column>
                <el-table-column
                    prop="noa"
                    label="NOA">
                </el-table-column>
                <el-table-column
                    prop="si"
                    label="SI">
                </el-table-column>
            </el-table>
        </el-card>

        <!--        <el-card class="box-card" style='width: 100%;display: flex'>-->
        <!--            <el-table v-if="LKresList.length"-->
        <!--                      :data="LKresList"-->
        <!--                      style="width: 100%">-->
        <!--                <el-table-column-->
        <!--                    prop="name"-->
        <!--                    label="类名">-->
        <!--                </el-table-column>-->
        <!--                <el-table-column-->
        <!--                    prop="cs"-->
        <!--                    label="CS">-->
        <!--                </el-table-column>-->
        <!--                <el-table-column-->
        <!--                    prop="noo"-->
        <!--                    label="NOO">-->
        <!--                </el-table-column>-->
        <!--                <el-table-column-->
        <!--                    prop="noa"-->
        <!--                    label="NOA">-->
        <!--                </el-table-column>-->
        <!--                <el-table-column-->
        <!--                    prop="si"-->
        <!--                    label="SI">-->
        <!--                </el-table-column>-->
        <!--            </el-table>-->
        <!--        </el-card>-->

        <el-card class="box-card">
            <el-collapse @change="handleChange">
                <el-collapse-item title="HELP" name="1">
                    <div>
                        <h2>1. CK度量阈值建议</h2>
                        <ol>
                            <li>
                                <strong>a) WMC(类的加权方法数):</strong> 一个较高的WMC值表明类可能过于复杂，难以理解和测试。通常认为，WMC超过20可能是一个警示信号，但这取决于方法的实际复杂度。
                            </li>
                            <li>
                                <strong>b) DIT(继承树的深度):</strong> DIT值过高（例如，超过5）可能表明过度的继承，这可能导致代码难以理解和维护。
                            </li>
                            <li>
                                <strong>c) NOC(子类数量):</strong> 较高的NOC值（如超过10）可能表示类的职责过多，可能需要进一步分解。
                            </li>
                            <li>
                                <strong>d) CBO(对象之间的耦合程度):</strong> CBO值超过10通常被视为高耦合，应考虑降低耦合。
                            </li>
                            <li>
                                <strong>e) RFC(类的响应集):</strong> 如果RFC超过50，可能表明类与系统的其他部分交互过于频繁，可能需要重新设计接口。
                            </li>
                            <li>
                                <strong>f) LCOM(类内聚缺乏度):</strong> LCOM较高（接近1）意味着类中的方法几乎没有共享数据，类的内聚性差，可能需要重构。
                            </li>
                            <li></li>
                        </ol>
                        <h2>2. LK度量阈值建议</h2>
                        <ul>
                            <li>
                                <strong>a) CS（类规模）:</strong> CS可以通过类中定义的方法的总数或者属性的总数来衡量，CS值过大反映出类承载的责任较多，在必要时考虑对富有多种职责的类进行分解。
                            </li>
                            <li>
                                <strong>b) NOO（方法重写数）:</strong> NOO值过大意味着类的设计可能存在问题，有可能是父类的抽象做的不好。
                            </li>
                            <li>
                                <strong>c) NOA（增加方法数）:</strong> NOA值过大意味着子类与父类（尤其祖先类）的特征相差越来越大。随着继承深度越来越深，NOA的值应该越小越好。
                            </li>
                            <li>
                                <strong>d) SI（特征化指数）:</strong> 特征化指数SI用来度量每一个子类在类图设计中的特征化等级，属于间接测量的综合指标。SI体现了这个类的个性，SI值过大，说明类不符合抽象层次结构。
                            </li>
                        </ul>
                    </div>

                </el-collapse-item>
            </el-collapse>
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
            CKAndLKList: [],
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
        // drawLineCK() {
        //     const myChart = echarts.init(this.$refs.mainck);
        //     let legendData = this.CKresList.map(item => {
        //         return item.name
        //     })
        //     let seriesData = this.CKresList.map(item => {
        //         return {
        //             value: [item.wmc, item.rfc, item.dit, item.noc, item.cbo, item.lcom],
        //             name: item.name
        //         }
        //     })
        //     var option = {
        //         title: {
        //             text: 'CK 度量'
        //         },
        //         legend: {
        //             data: legendData
        //         },
        //         radar: {
        //             // shape: 'circle',
        //             indicator: [
        //                 { name: 'WMC', max: 10 },
        //                 { name: 'RFC', max: 10 },
        //                 { name: 'DIT', max: 10 },
        //                 { name: 'NOC', max: 10 },
        //                 { name: 'CBO', max: 10 },
        //                 { name: 'LCOM', max: 10 },
        //             ]
        //         },
        //         series: [
        //             {
        //                 name: '',
        //                 type: 'radar',
        //                 data: seriesData
        //             }
        //         ]
        //     };
        //     myChart.setOption(option)
        // },
        // drawLineLK() {
        //     const myChart = echarts.init(this.$refs.mainlk);
        //     let legendData = this.LKresList.map(item => {
        //         return item.name
        //     })
        //     let seriesData = this.LKresList.map(item => {
        //         return {
        //             value: [item.cs, item.noo, item.noa, item.si],
        //             name: item.name
        //         }
        //     })
        //     var option = {
        //         title: {
        //             text: 'LK 度量'
        //         },
        //         legend: {
        //             data: legendData
        //         },
        //         radar: {
        //             // shape: 'circle',
        //             indicator: [
        //                 { name: 'CS', max: 10 },
        //                 { name: 'NOO', max: 10 },
        //                 { name: 'NOA', max: 10 },
        //                 { name: 'SI', max: 10 },
        //             ]
        //         },
        //         series: [
        //             {
        //                 name: '',
        //                 type: 'radar',
        //                 data: seriesData
        //             }
        //         ]
        //     };
        //     myChart.setOption(option)
        // },
        drawRadarChart(){
            const myChart = echarts.init(this.$refs.mainChart);
            let legendData = this.CKresList.map(item => {
                return item.name
            })
            let seriesDataCK = this.CKresList.map(item => {
                return {
                    value: [item.wmc, item.rfc, item.dit, item.noc, item.cbo, item.lcom],
                    name: item.name
                }
            })
            let seriesDataLK = this.LKresList.map(item => {
                return {
                    value: [item.cs, item.noo, item.noa, item.si],
                    name: item.name
                }
            })
            let seriesData = [];
            for (let i = 0; i < legendData.length; i++) {
                seriesData.push({
                    name: legendData[i],
                    value: seriesDataCK[i].value.concat(seriesDataLK[i].value)
                })
            }

            for (let i = 0; i < legendData.length; i++) {
                // 填充 CKAndLKList
                this.CKAndLKList.push({
                    wmc: seriesDataCK[i].value[0],
                    rfc: seriesDataCK[i].value[1],
                    dit: seriesDataCK[i].value[2],
                    noc: seriesDataCK[i].value[3],
                    cbo: seriesDataCK[i].value[4],
                    lcom: seriesDataCK[i].value[5],
                    cs: seriesDataLK[i].value[0],
                    noo: seriesDataLK[i].value[1],
                    noa: seriesDataLK[i].value[2],
                    si: seriesDataLK[i].value[3],
                    name: legendData[i]
                });
            }

            var option = {
                title: {
                    text: 'CK&LK 度量'
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
                        { name: 'LCOM', max: 10 },
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
            // this.drawLineCK()
            // this.drawLineLK()
            this.drawRadarChart()
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