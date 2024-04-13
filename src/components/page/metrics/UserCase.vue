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
                  <div>点击选择要上传的文件（oom 文件）</div>
              </div>
          </el-upload>
          <el-button style='margin-top: 10px' class="submit" type="primary" @click="uploadFile">上传文件</el-button>
      </el-card>


      <el-card class='box-card'>

          <el-empty
              v-if="!Object.keys(backData).length"
              description="请先上传文件"
          ></el-empty>
          <div class="getInfo" v-if="Object.keys(backData).length">
              <el-steps :active="active" finish-status="success">
                  <el-step
                      title="角色"
                      description="计算未调整前的角色权重UAW"
                  ></el-step>
                  <el-step
                      title="用例"
                      description="计算未调整前的用例权重UUC"
                  ></el-step>
                  <el-step
                      title="技术复杂因素"
                      description="技术会对软件规模造成影响"
                  ></el-step>
                  <el-step
                      title="环境因素"
                      description="环境也会对软件规模造成影响"
                  ></el-step>
                  <el-step
                      title="工作量与工作时间"
                      description="计算工作量与所需开发工作时间"
                  ></el-step>
              </el-steps>
              <el-card>
                  <!-- 角色 -->
                  <div v-if="active == 0">
                      <el-row style="margin-bottom:20px">
                          <el-table
                              :data="descTable.actor"
                              style="width: 100%"
                          >
                              <el-table-column prop="actorType" label="角色类别" width="100px">
                              </el-table-column>
                              <el-table-column prop="desc" label="说明">
                              </el-table-column>
                              <el-table-column prop="weight" label="权重" width="100px">
                              </el-table-column>
                          </el-table>
                      </el-row>
                      <h3>请根据上表说明选择角色的复杂度：</h3>
                      <el-row  v-for="(item, index) in backData.actor" :key="index" style="margin-bottom:10px">
                          <el-col :span="4">{{ item.name }}:</el-col>
                          <el-col :span="20">
                              <el-radio-group v-model="item.weight">
                                  <el-radio :label="1">简单</el-radio>
                                  <el-radio :label="2">普通</el-radio>
                                  <el-radio :label="3">复杂</el-radio>
                              </el-radio-group>
                          </el-col>
                      </el-row>
                  </div>
                  <!-- 用例 -->
                  <div v-if="active == 1">
                      <el-row style="margin-bottom:20px">
                          <el-table
                              :data="descTable.usercase"
                              style="width: 100%"
                          >
                              <el-table-column prop="actorType" label="角色类别" width="100px">
                              </el-table-column>
                              <el-table-column prop="desc" label="说明">
                              </el-table-column>
                              <el-table-column prop="weight" label="权重" width="100px">
                              </el-table-column>
                          </el-table>
                      </el-row>
                      <h3>请根据上表说明选择用例的复杂度：</h3>
                      <el-row v-for="(item, index) in backData.userCase" :key="index">
                          <el-col>{{ item.name }}:</el-col>
                          <el-col>
                              <el-radio-group v-model="item.weight">
                                  <el-radio :label="5">简单</el-radio>
                                  <el-radio :label="10">普通</el-radio>
                                  <el-radio :label="15">复杂</el-radio>
                              </el-radio-group>
                          </el-col>
                      </el-row>
                  </div>
                  <!-- 技术复杂性因素 -->
                  <div v-if="active == 2">
                      <!-- <el-row style="margin-bottom:20px">
                              <el-table
                              :data="descTable.tcf"
                            >
                              <el-table-column prop="actorType" label="技术复杂性Ti" width="120px">
                              </el-table-column>
                              <el-table-column prop="desc" label="说明">
                              </el-table-column>
                              <el-table-column prop="weight" label="权重WF" width="100px">
                              </el-table-column>
                            </el-table>
                        </el-row> -->
                      <el-row>
                          <h3>请选择您认为系统中存在的技术复杂性因素：</h3>
                          <p>影响程度说明：不选择表示不存在或者没有影响；1偶尔；2轻微；3中等；4显著；5强烈</p>
                          <!-- <el-checkbox-group v-model="checkTCFGroup">
                              <el-checkbox-button v-for="(item,index) in descTable.tcf" :label="item" :key="index">{{item.desc}}</el-checkbox-button>
                          </el-checkbox-group> -->
                      </el-row>
                      <el-row>
                          <div v-for="(item,index) in descTable.tcf" :key="index">
                              <i>{{item.desc}}</i>
                              <el-rate
                                  v-model="item.level"
                                  show-text
                                  :texts="['偶尔','轻微','中等','显著','强烈']">
                              </el-rate>
                          </div>
                      </el-row>
                  </div>
                  <!-- 技术复杂性因素 -->
                  <div v-if="active == 3">
                      <!-- <el-row style="margin-bottom:20px">
                          <el-table
                            :data="descTable.ef"
                            style="width: 100%"
                          >
                            <el-table-column prop="actorType" label="环境复杂性Fi" width="200px">
                            </el-table-column>
                            <el-table-column prop="desc" label="说明">
                            </el-table-column>
                            <el-table-column prop="weight" label="权重Wi" width="100px">
                            </el-table-column>
                          </el-table>
                        </el-row> -->
                      <el-row>
                          <h3>请选择您认为系统中存在的环境复杂性因素：</h3>
                          <p>影响程度说明：不选择表示不存在或者没有影响；1偶尔；2轻微；3中等；4显著；5强烈</p>
                          <!-- <el-checkbox-group v-model="checkTCFGroup">
                              <el-checkbox-button v-for="(item,index) in descTable.tcf" :label="item" :key="index">{{item.desc}}</el-checkbox-button>
                          </el-checkbox-group> -->
                      </el-row>
                      <el-row>
                          <div v-for="(item,index) in descTable.ef" :key="index">
                              <i>{{item.desc}}</i>
                              <el-rate
                                  v-model="item.level"
                                  show-text
                                  :texts="['偶尔','轻微','中等','显著','强烈']">
                              </el-rate>
                          </div>
                      </el-row>
                  </div>
                  <!-- 工作量与时间 -->
                  <div v-if="active==4">
                      <el-row :gutter="20">
                          <el-col :span="10">
                              <h4 style="margin:auto;margin-top:10px ">请输入一个用例点花费的时间（单位：人时/UPC)</h4>
                          </el-col>
                          <el-col :span="14">
                              <el-input
                                  class="input"
                                  v-model="UCTime"
                                  placeholder="请输入一个用例点花费的时间（单位：人时/UPC)"
                              ></el-input>
                          </el-col>
                      </el-row>
                      <el-row :gutter="20">
                          <el-col :span="10">
                              <h4 style="margin:auto;margin-top:10px ">请输入一个月的工作时间（单位：小时)</h4>
                          </el-col>
                          <el-col :span="14">
                              <el-input
                                  class="input"
                                  v-model="workTime"
                                  placeholder="请输入一个月的工作时间（单位：小时)"
                              ></el-input>
                          </el-col>
                      </el-row>
                  </div>
                  <!-- 结果 -->
                  <div v-if="active==5">
                      <h3>用例点度量结果如下</h3>
                      <el-table v-if="tableData.length" :data="tableData" style="width: 100%">
                          <el-table-column prop="UAW" label="UAW"> </el-table-column>
                          <el-table-column prop="UUC" label="UUC"> </el-table-column>
                          <el-table-column prop="UUCP" label="UUCP"> </el-table-column>
                          <el-table-column prop="TCF" label="TCF"> </el-table-column>
                          <el-table-column prop="EF" label="EF"> </el-table-column>
                      </el-table>
                      <p><strong>用例点 UPC:</strong> {{ UPC }}</p>

                      <el-row>
                          实现该类需要的工作量Effort（单位：小时）：
                          <el-input v-model="effort" :disabled="true"> </el-input>
                      </el-row>
                      <el-row style="margin-top: 10px">
                          实现该类需要（单位：人月）：
                          <el-input v-model="time" :disabled="true"> </el-input>
                      </el-row>
                  </div>
              </el-card>
              <el-button v-if="active!=5"
                         style="margin-top: 12px; margin-left: 400px; float: left"
                         @click="prev"
              >上一步</el-button
              >
              <el-button v-if="active!=5"
                         style="margin-top: 12px; margin-right: 400px; float: right"
                         @click="next"
              >下一步</el-button
              >
          </div>
      </el-card>
  </el-container>
</template>

<script>
export default {
  name: "UserCase",
  data() {
    return {
      fileList: [],
      descTable: {
        actor: [
          {
            actorType: "简单",
            desc: "角色代表已定义好应用程序编程接口(API)的另外一个系统",
            weight: 1 ,
          },
          {
            actorType: "普通",
            desc: "角色代表一个通过协议，如TCP/IP相互作用的另一个系统",
            weight:2,
          },
          {
            actorType: "复杂",
            desc: "角色是通过人机交互界面进行交互的人",
            weight:3,
          },
        ],
        usercase: [
          {
            actorType: "简单",
            desc: "仅涉及1个数据库实体(表或视图) ;或者操作数量不超过3个;或者实现过程中用到5个以下的分析类。",
            weight: 5,
          },
          {
            actorType: "普通",
            desc: "仅涉及2个数据库实体(表或视图) ;或者操作数量在5~7个;或者实现过程中用到5~10个的分析类。",
            weight:10,
          },
          {
            actorType: "复杂",
            desc: "涉及3个以上的数据库实体(表或视图) ;或者操作数量超过7个;或者实现过程中用到10个以上的分析类。",
            weight:15,
          },
        ],
        tcf: [
          {
            actorType: "T1",
            desc: "分布式系统",
            weight:2,
            level:0
          },
          {
            actorType: "T2",
            desc: "响应或吞吐量性能",
            weight:1,
            level:0
          },
          {
            actorType: "T3",
            desc: "终端用户效率",
            weight:1,
            level:0
          },
          {
            actorType: "T4",
            desc: "复杂的内部处理",
            weight:1,
            level:0
          },
          {
            actorType: "T5",
            desc: "代码必须重用",
            weight:1,
            level:0
          },
          {
            actorType: "T6",
            desc: "易安装性",
            weight:0.5,
            level:0
          },
          {
            actorType: "T7",
            desc: "易用性",
            weight:0.5,
            level:0
          },
          {
            actorType: "T8",
            desc: "可移植性",
            weight:2,
            level:0
          },
          {
            actorType: "T9",
            desc: "易更改性",
            weight:1,
            level:0
          },
          {
            actorType: "T10",
            desc: "并发性",
            weight: 1,
            level:0
          },
          {
            actorType: "T11",
            desc: "特殊的安全性",
            weight: 1,
            level:0
          },
          {
            actorType: "T12",
            desc: "提供第三方接口",
            weight: 1,
            level:0
          },
          {
            actorType: "T13",
            desc: "需要特别的用户培训",
            weight: 1,
            level:0
          },
        ],
        ef: [
            {
                actorType: "F1",
                desc: "熟悉UML程度",
                weight: 1.5,
            level:0
            },
            {
                actorType: "F2",
                desc: "开发应用程序的经验",
                weight: 0.5,
            level:0
            },
            {
                actorType: "F3",
                desc: "面向对象的经验",
                weight: 1,
            level:0
            },
            {
                actorType: "F4",
                desc: "主分析师能力",
                weight: 0.5,
            level:0
            },
            {
                actorType: "F5",
                desc: "激励机制",
                weight: 1,
            level:0
            },
            {
                actorType: "F6",
                desc: "需求稳定度",
                weight: 2,
            level:0
            },
            {
                actorType: "F7",
                desc: "具有兼职人员",
                weight: -1,
            level:0
            },
            {
                actorType: "F8",
                desc: "具有复杂编程",
                weight: -1,
            level:0
            },
        ]
      },
      backData: {},
      active: 0, // 当前活跃step
      checkTCFGroup: [],
      checkEFGroup: [],
      tableData: [],
      UPC: 90.17,
      UCTime: null,
      workTime: null,
      effort: 0,
      time: 0,
    };
  },
  methods: {
    onChange(file, fileList) {
      console.log(file);
      console.log(fileList);
      this.fileList = fileList;
    },
    async uploadFile() {
      let formData = new FormData();
      for (let i in this.fileList) {
        formData.append("file", this.fileList[i].raw);
      }
      let res = await this.axios({
        url: "http://localhost:8080/oom/uploadoom",
        method: "post",
        data: formData,
      });
      console.log(res);
      let { data } = await this.axios({
        url: "http://localhost:8080/oom/userCaseDetail",
        method: "get",
      });
      let {actorList:actor, useCaseList:userCase} = data.data;
      this.backData = {
        actor,userCase
      }
      this.backData = {...this.backData}
      console.log('back',this.backData);
    },
    // 溢出则替换
    onExceed(files, fileList) {
      this.fileList.pop();
      this.fileList.push(files[0]);
    },
    prev() {
      if (this.active-- < 1) this.active = 0;
    },
    next() {
      if (this.active++ > 3) {
        // 计算最终结果
        console.log('计算最终结果');
        // 1.UUCP
        console.log(this.backData);
        let UAW=0,UUC=0,UUCP=0;
        this.backData.actor.forEach(item=>UAW+=item.weight);
        this.backData.userCase.forEach(item=>UUC+=item.weight);
        UUCP=UAW+UUC;
        // 2.TCF\EF
        let TCF=0,EF=0;
        console.log(this.descTable);
        this.descTable.tcf.forEach(item=>TCF+=item.weight*item.level);
        this.descTable.ef.forEach(item=>EF+=item.weight*item.level);
        console.log('prev',TCF,EF);
        TCF = (0.6+(0.01*TCF)).toFixed(2);
        EF = (1.4+(-0.03*EF)).toFixed(2);
        console.log('next',TCF,EF);
        // this.effort = this.UPC * this.UCTime;
        // this.time = Math.round(this.effort / this.workTime);
        this.tableData = [{UAW,UUC,UUCP,TCF,EF}];
        this.UPC = (UUCP*TCF*EF).toFixed(2);
        this.effort = (this.UPC * this.UCTime).toFixed(2);
        this.time = (this.effort / this.workTime).toFixed(2);
      }
    },
  },
};
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