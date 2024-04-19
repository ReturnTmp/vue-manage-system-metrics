<template>
    <el-container style='display: block'>
        <el-card class="box-card" style='line-height: 30px'>
            <h3>信息流度量页面</h3>
            <p>在本页面中上传源代码，我们将根据信息流度量的方法得出度量结果</p>
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
            <el-table v-if="tableData.length" :data="tableData" style="width: 100%">
                <el-table-column prop="methodName" label="方法名" width="180"></el-table-column>
                <el-table-column prop="flowIn" label="扇入"></el-table-column>
                <el-table-column prop="flowOut" label="扇出"></el-table-column>
                <el-table-column prop="length" label="方法长度"></el-table-column>
                <el-table-column prop="complexity" label="复杂度"></el-table-column>
                <el-table-column prop="shepperdComplexity" label="Shepperd复杂度"></el-table-column>
<!--                <el-table-column prop="startLine" label="起始行"></el-table-column>-->
<!--                <el-table-column prop="endLine" label="结束行"></el-table-column>-->
            </el-table>

        </el-card>

        <el-card class="box-card" v-if='tableData.length' style='width: 100%'>
            <div style="height:500px;width: 100%;" ref="main"></div>
        </el-card>


    </el-container>
</template>

<script>
import * as echarts from 'echarts';
export default {
    name:'InfoFlow',
    data() {
        return {
            fileList: [],
            tableData: [],
            activeName: 'first',
            textarea: ''
        }
    },
    methods: {
        drawLine() {
            const myChart = echarts.init(this.$refs.main);
            let legendData = this.tableData.map(item => {
                return item.methodName
            })
            let seriesData = this.tableData.map(item => {
                return {
                    value: [item.flowIn, item.flowOut, item.length, item.complexity, item.shepperdComplexity, item.startLine, item.endLine],
                    name: item.methodName
                }
            })
            var option = {
                title: {
                    text: '信息流度量'
                },
                legend: {
                    data: legendData
                },
                radar: {
                    // shape: 'circle',
                    indicator: [
                        { name: 'flowIn', max: 10 },
                        { name: 'flowOut', max: 10 },
                        { name: 'length', max: 10 },
                        { name: 'complexity', max: 150 },
                        { name: 'shepperdComplexity', max: 100 },
                        { name: 'startLine', max: 40 },
                        { name: 'endLine', max: 40 },
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
            console.log('上传文件');
            let formData = new FormData();
            for (let i in this.fileList) {
                formData.append('txt', this.fileList[i].raw);
            }
            let res = await this.axios({
                url:'http://localhost:8080/flow/uploadtxt',
                method: 'post',
                data: formData
            });
            console.log(res);
            let {data} = await this.axios({
                url: 'http://localhost:8080/flow/getInfoFlow',
                method: 'get',
            })
            console.log('data',data.data);
            this.tableData = data.data;
            console.log('tableData',this.tableData);
            // 雷达图
            this.$nextTick(()=>{
                this.drawLine();
            })
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