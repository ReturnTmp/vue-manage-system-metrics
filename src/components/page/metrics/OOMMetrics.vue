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
                                <strong>a) 类的加权方法数 (WMC):</strong> 一个较高的WMC值表明类可能过于复杂，难以理解和测试。通常认为，WMC超过20可能是一个警示信号，但这取决于方法的实际复杂度。
                            </li>
                            <li>
                                <strong>b) 深度继承树 (DIT):</strong> DIT值过高（例如，超过5）可能表明过度的继承，这可能导致代码难以理解和维护。
                            </li>
                            <li>
                                <strong>c) 子类数量 (NOC):</strong> 较高的NOC值（如超过10）可能表示类的职责过多，可能需要进一步分解。
                            </li>
                            <li>
                                <strong>d) 耦合程度 (CBO):</strong> CBO值超过10通常被视为高耦合，应考虑降低耦合。
                            </li>
                            <li>
                                <strong>e) 响应集 (RFC):</strong> 如果RFC超过50，可能表明类与系统的其他部分交互过于频繁，可能需要重新设计接口。
                            </li>
                            <li>
                                <strong>f) 缺乏内聚力 (LCOM):</strong> LCOM较高（接近1）意味着类中的方法几乎没有共享数据，类的内聚性差，可能需要重构。
                            </li>
                            <li></li>
                        </ol>
                        <h2>2. LK度量阈值建议</h2>
                        <ul>
                            <li>
                                <strong>a) CS（类大小）:</strong> 通常认为，一个类的代码行数不应超过几百行，具体数字可能因语言和项目类型而异。例如，超过200行可能需要审查和可能的重构。
                            </li>
                            <li>
                                <strong>b) NOO（操作重载度）:</strong> 较高的NOO可能表明过度使用继承，如果NOO接近父类的操作总数，可能表明设计上的问题。
                            </li>
                            <li>
                                <strong>c) NOA（属性数）:</strong> 属性数量过多（例如超过10-15）可能表明类的职责过于庞大，考虑是否违反了单一职责原则。
                            </li>
                            <li>
                                <strong>d) SI（专用性指数）:</strong> 没有固定的好坏标准，但是一个较高的SI值需要特别关注，因为它可能表明类的设计高度依赖继承。
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