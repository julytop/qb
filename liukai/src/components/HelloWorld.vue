<template>
  <div class="hello">
    <el-row :gutter="20">
      <el-col :span="7">
        <div class="grid-content bg-purple">
          一级类别：
          <el-select v-model="fircategory" placeholder="请选择" @change='changefircate'>
            <el-option v-for="item in firoptions" :key="item.id" :label="item.name" :value="item.id">
            </el-option>
          </el-select>
        </div>
      </el-col>
      <el-col :span="7">
        <div class="grid-content bg-purple">
          二级类别：
          <el-select v-model="seccategory" style="margin-left: 20px;" placeholder="请选择" @change='changeseccate'>
            <el-option v-for="item in secoptions" :key="item.id" :label="item.name" :value="item.id">
            </el-option>
          </el-select>
        </div>
      </el-col>
    </el-row>
    <br>
    <el-table :data="tableData" style="width: 100%" align='center'>
      <el-table-column prop="id" label="ID">
      </el-table-column>
      <el-table-column prop="title" label="标题">
      </el-table-column>
      <el-table-column prop="created_time" label="创建时间">
      </el-table-column>
      <el-table-column prop="content" label="内容">
      </el-table-column>
      <el-table-column prop="name" label="一级列别">
      </el-table-column>
      <el-table-column prop="address" label="二级类别">
      </el-table-column>
    </el-table>

  </div>
</template>

<script>
  import axios from 'axios';
  export default {
    name: 'HelloWorld',
    data() {
      return {
        fircategory: '',
        seccategory: '',
        secquery: '1',
        listquery: '1',
        tableData: [],
        firoptions: [],
        secoptions: [],
      }
    },
    // filters: {
    //   isshowtime(time) {
    //     debugger;
    //     this.showtime(time)
    //   }
    // },
    methods: {
      getfircate() {
        axios.get('http://interview-test.ipmapp.cn/api/v1/classify?parent=0').then((response) => {
          this.firoptions = response.data.data;
        });
      },
      getseccate() {
        axios.get('http://interview-test.ipmapp.cn/api/v1/classify?parent=' + this.secquery).then((response) => {
          this.secoptions = response.data.data;
        });
      },
      getlist() {
        let that=this;
        axios.get('http://interview-test.ipmapp.cn/api/v1/record?classify=' + that.listquery).then((response) => {
          that.tableData = response.data.data;
          //没有element-ui中找到可以写过滤的办法，只能。。。
          for(let i=0;i<this.tableData.length;i++){
            let time=that.showtime(this.tableData[i].created_time);
            that.$set(this.tableData[i],'created_time',time)
          }
        });
      },
      //一级列别
      changefircate() {
        this.secquery = this.fircategory;
        this.getseccate(); //二级列别联动
        this.listquery = this.fircategory;
        this.getlist(); //列表联动
      },
      changeseccate() {
        this.listquery = this.seccategory;
        this.getlist();
      },
      //显示时间
      showtime(d1) {
        var dateBegin = new Date(d1.replace(/-/g, "/")); //将-转化为/，使用new Date
        var dateEnd = new Date(); //获取当前时间
        var dateDiff = dateEnd.getTime() - dateBegin.getTime(); //时间差的毫秒数
        var dayDiff = Math.floor(dateDiff / (24 * 3600 * 1000)); //计算出相差天数
        var leave1 = dateDiff % (24 * 3600 * 1000) //计算天数后剩余的毫秒数
        var hours = Math.floor(leave1 / (3600 * 1000)) //计算出小时数
        //计算相差分钟数
        var leave2 = leave1 % (3600 * 1000) //计算小时数后剩余的毫秒数
        var minutes = Math.floor(leave2 / (60 * 1000)) //计算相差分钟数
        //计算相差秒数
        var leave3 = leave2 % (60 * 1000) //计算分钟数后剩余的毫秒数
        var seconds = Math.round(leave3 / 1000)
        var time= dayDiff + "天前 " + hours + "小时前创建 ";
        return time;
      }
    },
    created: function () {
      this.getlist(); //获取列表
      this.getfircate();
      this.getseccate();
    },
  }
</script>

<style scoped>
</style>