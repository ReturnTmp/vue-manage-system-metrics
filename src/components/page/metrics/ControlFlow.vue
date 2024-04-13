<template>
  <el-container class="body">

    <el-main style="overflow:auto; height:400px">

      <el-empty v-if="!tableData.length" description="请先上传文件"></el-empty>
      <el-table v-if="tableData.length"
                :data="tableData"
                style="width: 100%">

        <el-table-column
          prop="decisionNodeCount"
          label="判定节点数">
        </el-table-column>
        <el-table-column
          prop="ordinaryNodeCount"
          label="普通节点数">
        </el-table-column>
        <el-table-column
          prop="totalNodeCount"
          label="总节点数">
        </el-table-column>
        <el-table-column
          prop="lineCount"
          label="连线数量">
        </el-table-column>
        <el-table-column
          prop="regionCount"
          label="区域数量">
        </el-table-column>
        <el-table-column
          prop="cyclomaticComplexity"
          label="圈复杂度">
        </el-table-column>

      </el-table>

    </el-main>
  </el-container>
</template>

<script>
export default {
  name:'ContorlFlow',
  data() {
    return {
      fileList: [],
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
      let res = await this.axios({
        url:'http://localhost:8080/oom/uploadoom',
        method: 'post',
        data: formData
      });
      console.log(res);
      let {data} = await this.axios({
        url: 'http://localhost:8080/oom/controlFlow',
        method: 'get',
      })
      console.log('获得',data);
      this.tableData=[{...data.data}];
    },
    // 溢出则替换
    onExceed(files,fileList){
      this.fileList.pop();
      this.fileList.push(files[0]);
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
.submit-button {
    margin-top: 10px;
    margin-left: 70px;
}

</style>
