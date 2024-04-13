<template>
    <el-container style='display: block'>

        <el-card class="box-card" style='line-height: 30px'>
            <h3>功能点度量页面</h3>
            <p>在本页面设置各类功能点数量和系统特征，将根据功能点度量的方法得出度量结果</p>
        </el-card>

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
                <div class="el-upload__tip">请上传xml文件</div>
            </el-upload>
            <el-button class="submit" type="primary" round @click="uploadFile">上传文件</el-button>
        </el-card>


        <el-card class="box-card">
            <el-steps :active="stepNumber" align-center>
                <el-step title="步骤1" description="填写功能点数量"></el-step>
                <el-step title="步骤2" description="填写技术因子影响程度"></el-step>
                <el-step title="步骤3" description="完成"></el-step>
            </el-steps>

            <div v-if='stepNumber == 1'>
                <p style='margin-top: 20px'>当前计算UFC的结果为：{{UFCNumber}}</p>
                <el-divider></el-divider>
                <el-table :data="tableDataUFC" border style="width: 100%">
                    <el-table-column prop="date" label="日期" width="180">
                        <template slot-scope="scope">
                            <input type="text" v-model="scope.row.date" v-show="scope.row.iseditor" />
                            <span v-show="!scope.row.iseditor">{{scope.row.date}}</span>
                        </template>
                    </el-table-column>
                    <el-table-column prop="name" label="姓名" width="180">
                        <template slot-scope="scope">
                            <input type="text" v-model="scope.row.name" v-show="scope.row.iseditor" />
                            <span v-show="!scope.row.iseditor">{{scope.row.name}}</span>
                        </template>
                    </el-table-column>
                    <el-table-column prop="address" label="地址">
                        <template slot-scope="scope">
                            <input type="text" v-model="scope.row.address" v-show="scope.row.iseditor" />
                            <span v-show="!scope.row.iseditor">{{scope.row.address}}</span>
                        </template>
                    </el-table-column>
                    <el-table-column label="操作" width="180">
                        <template slot-scope="scope">
                            <el-button type="warning" @click="edit(scope.row, scope)">编辑</el-button>
                            <el-button type="danger" @click="save(scope.row)">保存</el-button>
                        </template>
                    </el-table-column>
                    </el-table>

                    <el-divider></el-divider>
                <div  style='display: flex;justify-content:center'>
                    <el-button type="primary" @click="stepNumber = 2">下一步</el-button>
                </div>
            </div>

            <div v-if='stepNumber == 2'>
                <div style='display: flex;justify-content:center'>
                    <el-button type="primary" @click="stepNumber = 1">上一步</el-button>
                    <el-button type="primary" @click="stepNumber = 3">下一步</el-button>
                </div>
            </div>

            <div v-if='stepNumber == 3'>
                <div style='justify-content:center'>
                    <el-result icon="success" :title="'功能点度量结果为：' + metricsResult " subTitle="可以点击“上一步”修改项目参数">
                    </el-result>
                </div>
                <div style='display: flex;justify-content: center'>
                    <el-button type="primary" @click="stepNumber = 2">上一步</el-button>
                </div>

            </div>
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

        <el-card  class="box-card">
            <el-collapse @change="handleChange">
                <el-collapse-item title="HELP" name="1">
                    <p style="font-weight: bold; color: #333;">我们规定圈复杂度在[1, 10]之间为良好，[11, +∞]为过大。</p>

                    <p style="margin-top: 20px;">圈复杂度良好提示：该模块圈复杂度良好，表示该模块复杂程度适中。</p>

                    <p style="margin-top: 20px;">圈复杂度过大提示：该模块圈复杂度过大。一个模块过于复杂可能会带来软件阅读和理解难度增加、维护成本提高、测试难度增加、可靠性下降等问题。您可以考虑通过重构代码、简化条件逻辑、增加代码的模块化等方式来降低模块的复杂度。详细的降低圈复杂度的方法见<a href="#">这里</a>。</p>

                    <p style="margin-top: 20px; font-weight: bold;">详细的降低圈复杂度的方法：</p>

                    <ul style="margin-top: 10px;">
                        <li style="margin-top: 10px;">降低圈复杂度的方法
                            <ul style="margin-top: 5px;">
                                <li style="margin-top: 5px;">重构代码
                                    <ul style="margin-top: 5px;">
                                        <li style="margin-top: 5px;">分解复杂函数：将复杂的函数分解成几个更小、更专一的函数。</li>
                                        <li style="margin-top: 5px;">使用多态替代条件语句：在适用的情况下，可以通过多态来替代过多的if-else或switch-case语句。</li>
                                    </ul>
                                </li>
                                <li style="margin-top: 5px;">简化条件逻辑
                                    <ul style="margin-top: 5px;">
                                        <li style="margin-top: 5px;">简化条件表达式：通过逻辑运算简化复杂的条件表达式。</li>
                                        <li style="margin-top: 5px;">避免嵌套控制流：减少if、for、while等控制流结构的嵌套深度。</li>
                                    </ul>
                                </li>
                                <li style="margin-top: 5px;">使用设计模式
                                    <ul style="margin-top: 5px;">
                                        <li style="margin-top: 5px;">策略模式：对于有多个分支的决策逻辑，可以使用策略模式将每个分支的逻辑封装到单独的类中。</li>
                                        <li style="margin-top: 5px;">状态模式：对于复杂的状态机逻辑，使用状态模式可以帮助管理不同状态下的行为，降低复杂度。</li>
                                    </ul>
                                </li>
                                <li style="margin-top: 5px;">优化循环和递归
                                    <ul style="margin-top: 5px;">
                                        <li style="margin-top: 5px;">减少循环内部的决策逻辑：尽量保持循环体简洁，将复杂的逻辑移出循环。</li>
                                        <li style="margin-top: 5px;">谨慎使用递归：递归可以使某些算法看起来更简洁，但也可能增加复杂度和资源消耗。在可能的情况下，考虑使用迭代代替递归。</li>
                                    </ul>
                                </li>
                                <li style="margin-top: 5px;">增加代码的模块化
                                    <ul style="margin-top: 5px;">
                                        <li style="margin-top: 5px;">模块化设计：通过模块化设计，将程序划分成高内聚、低耦合的模块，每个模块专注于完成一个具体的任务。</li>
                                    </ul>
                                </li>
                            </ul>
                        </li>
                    </ul>
                </el-collapse-item>
            </el-collapse>
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
        },
        edit(row, index) {
            row.iseditor = true;
        },
        save(row, index) {
            row.iseditor = false;
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