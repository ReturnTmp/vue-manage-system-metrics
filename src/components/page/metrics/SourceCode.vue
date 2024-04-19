<template>
    <el-container style='display: block'>
        <el-card class="box-card" style='line-height: 30px'>
            <h3>源代码度量页面</h3>
            <p>在本页面中上传源代码，我们将为您分析源代码的代码行数、空白行数、注释行数和注释密度，并为您提供相应的代码优化建议</p>
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
<!--                <el-col :span="4" class='col-box'>-->
<!--                    <el-card :body-style="{ padding: '0px' }">-->
<!--                        <div style="padding: 14px;">-->
<!--                            <i class="el-icon-s-opportunity" style='font-size: 20px;margin-top: 10px;margin-left: 20px;margin-right: 10px'></i>-->

<!--                            <span style='font-size: 20px;font-weight: bold'>圈复杂度</span>-->
<!--                            <div class="bottom clearfix">-->
<!--                                {{tableData[0].cycleComplexity}}-->
<!--                            </div>-->
<!--                        </div>-->
<!--                    </el-card>-->
<!--                </el-col>-->
            </el-row>

        </el-card>

        <el-card class="box-card" v-if='tableData.length' style='width: 100%'>
            <div style="height:500px;width: 100%;" ref="main"></div>
        </el-card>
<!--        根据注释密度给出合理帮助建议-->
        <el-card class="box-card" v-if='tableData.length'>
<!--            <el-collapse @change="handleChange">-->
<!--                <el-collapse-item title="HELP" name="1">-->
<!--                </el-collapse-item>-->
<!--            </el-collapse>-->
            <div v-if='tableData[0].commentDensity < 0.1'>
                <p>
                    注释不够提示：您的代码注释比例不高。注释比例不高可能会带来代码理解难度增加、维护成本增加、沟通成本增加和测试和验证困难等问题。注释是代码可维护性和可理解性的重要组成部分。所以说适当、精准的注释对于维护大型或复杂项目至关重要，您可以考虑适当添加注释来提高您的代码注释比例。
                </p>
            </div>
            <div v-else-if='tableData[0].commentDensity > 0.5'>
                <p>
                    注释比例过高提示：您的代码注释比例过高。过多的注释可能会分散代码阅读者的注意力，使得他们的关注点不在代码上而转移到注释上面，从而可能使他们忽略代码本身的问题。同时，过多的注释也会增加代码阅读者理解代码的成本，大量的注释可能会淹没实际的代码，使得阅读和理解代码本身变得更加困难。您可以考虑适当精简代码来改善这个问题。
                </p>
            </div>
            <div v-else>
                <p>
                    注释比例良好提示：您的代码注释比例良好。当代码的注释比例良好时，注释既充分又高效地传达了代码的意图和复杂性。良好的注释提高了代码的可读性，简化了代码的维护，提高了项目的开发效率，有利于代码的可持续发展。
                </p>
            </div>
        </el-card>

        <el-card  class="box-card">
            <el-collapse @change="handleChange">
                <el-collapse-item title="HELP" name="1">
                    <p>代码的注释比例是衡量软件可理解性和可维护性的一个重要的指标。</p>
                    <p>好的代码应该是自描述的，代码自己就已经说明问题了。在难以理解的地方才加上注释。</p>
                    <p>好的注释应该具备以下几种特性：</p>
                    <ul>
                        <h2>1. 清晰性</h2>
                        <ul>
                            <li>a) 注释应该是清晰和简洁的，避免不必要的复杂性和冗长。</li>
                            <li>b) 使用简单直接的语言描述代码的目的和行为。</li>
                        </ul>
                        <h2>2. 精确</h2>
                        <ul>
                            <li>a) 注释应该准确反映代码的目的和功能。</li>
                            <li>b) 避免提供可能随时间变得不准确或误导性的信息。</li>
                        </ul>
                        <h2>3. 相关性</h2>
                        <ul>
                            <li>a) 注释应当与其描述的代码紧密相关，避免包含与代码无关的信息。</li>
                            <li>b) 聚焦于解释代码的“为什么”（即背后的逻辑或原因），而非“怎么做”的细节，后者应该通过清晰的代码表达。</li>
                        </ul>
                        <h2>4. 必要性</h2>
                        <ul>
                            <li>a) 只在必要时添加注释。过度的注释可能会干扰代码的阅读流程，而且维护注释也需要时间和资源。</li>
                            <li>b) 对于自解释的代码段，尽量避免添加注释。如果代码已经足够清晰，再添加注释可能会导致信息重复。</li>
                        </ul>
                        <h2>5. 一致性</h2>
                        <ul>
                            <li>a) 在整个代码库中使用一致的注释风格和约定，这包括注释的格式、位置和用词。</li>
                            <li>b) 团队应该共同决定注释的标准，并遵守这些标准以保持代码库的一致性。</li>
                        </ul>
                    </ul>
                </el-collapse-item>
            </el-collapse>
        </el-card>

    </el-container>
</template>

<script>
import * as echarts from 'echarts';
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
        drawLine() {
            const myChart = echarts.init(this.$refs.main);
            var option = {
                title: {
                    text: '注释密度：'+ (this.tableData[0].commentDensity * 100).toFixed(3) + '%',
                },
                tooltip: {
                    trigger: 'item'
                },
                legend: {
                    top: '5%'
                },
                label: {
                    show: false
                },
                series: [
                    {
                        name: '源代码度量',
                        type: 'pie',
                        radius: ['40%', '70%'],
                        avoidLabelOverlap: false,
                        itemStyle: {
                            borderRadius: 10,
                            borderColor: '#fff',
                            borderWidth: 2
                        },
                        label: {
                            show: false,
                            position: 'center'
                        },
                        emphasis: {
                            label: {
                                show: true,
                                fontSize: 40,
                                fontWeight: 'bold'
                            }
                        },
                        labelLine: {
                            show: false
                        },
                        data: [
                            { value: this.tableData[0].codeLines - this.tableData[0].commentLines, name: '未注释行数' },
                            { value: this.tableData[0].commentLines, name: '注释行数' },
                        ]
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