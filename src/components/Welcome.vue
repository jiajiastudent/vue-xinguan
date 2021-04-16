<template>
  <div id="welcome">
    <el-breadcrumb separator="/" style="padding-left:10px;padding-bottom:10px;font-size:12px;">
      <el-breadcrumb-item :to="{ path: '/home' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>欢迎</el-breadcrumb-item>
    </el-breadcrumb>

    <el-row :gutter="15">
      <!-- 左边部分 -->
      <el-col :span="12">
        <el-row :gutter="30" style="margin-bottom:0;">
          <!--  用户信息,角色-->
          <el-col :span="3.5" ><el-card class="grid-content bg-purple" style="height: 110px;">
            <div>
              <label style="font-size:20px;color:gray">USER&nbsp</label>
              <label style="font-size:20px;">{{tableInfo[0].username}}</label>
            </div>
            <div>
              <label style="font-size:20px;color:gray">&nbspROLR&nbsp</label>
              <label style="font-size:20px;">{{tableInfo[0].roles}}</label>
            </div>
          </el-card></el-col>
          <!--      菜单四个图标-->
          <el-col :span="5.8"><el-card class="grid-content bg-purple" style="height:110px;font-size:20px;color:gray" >
            <el-row style="margin-top:0px;" :gutter="20">
              <el-col :span="6">
                <router-link to="/products">
                  <img src="../assets/a1.svg" alt width="40.8px;"/>
                </router-link>
                <div style="font-size:12px;margin-left:0px;">物资资料</div>
              </el-col>
              <el-col :span="6">
                <div class="grid-content bg-purple-light">
                  <router-link to="/stocks">
                    <img src="../assets/a2.svg" alt width="40.8px;" style="cursor: pointer;margin-left:0px;"/>
                  </router-link>
                  <div style="font-size:12px;margin-left:0px;">物资库存</div>
                </div>
              </el-col>
              <el-col :span="6">
                <div class="grid-content bg-purple-light">
                  <router-link to="/inStocks">
                    <img src="../assets/a3.svg" alt width="40.8px;" style="cursor: pointer;margin-left:0px;"/>
                  </router-link>
                  <div style="font-size:12px;margin-top:0px;margin-left:0px;">物资入库</div>
                </div>
              </el-col>
              <el-col :span="6">
                <div class="grid-content bg-purple"></div>
                <router-link to="/outStocks">
                  <img src="../assets/a4.svg" alt width="40.8px;" style="cursor: pointer;margin-left:0px;"/>
                </router-link>
                <div style="font-size:12px;margin-top:0px;margin-left:0px;">物资发放</div>
              </el-col>
            </el-row>
          </el-card></el-col>
          <!--    疫情病例数据-->
          <el-col :span="2.5">
            <el-card  class="grid-content bg-purple" style="height:110px;font-size:20px;color:gray;">
              <label style="font-size:15px; font-weight: 600">现存确诊</label>
              <div>
                <label style=";font-size:30px ;font-weight: 500;border: 0px;color:rgb(174, 33, 44)">{{daily}}</label>
                <!--            info[0].value-->
              </div>
              <label style="font-size:15px;">较昨日</label>
              <label style="font-size:15px;color:rgb(174, 33, 44)">{{add_daily}}</label>
              <!--          {{info[0].yesterday}}-->
            </el-card></el-col>
          <el-col :span="2.5">
            <el-card  class="grid-content bg-purple" style="height:110px;font-size:20px;color:gray;">
              <label style="font-size:15px; font-weight: 600">现存疑似</label>
              <div>
                <label style=";font-size:30px ;font-weight: 500;border: 0px;color:rgb(247, 130, 7)">{{sustotal}}</label>
              </div>
              <label style="font-size:15px;">较昨日</label>
              <label style="font-size:15px;color:rgb(247, 130, 7)">{{add_sustotal}}</label>
            </el-card></el-col>
        </el-row>
        <!--条形图-->
        <el-row>
          <el-card class="box-card" style="margin-top:15px;">
            <!-- 为条形图 EChartst 准备一个具备大小（宽高）的 DOM -->
            <div id="tianxing" style="width: 800px;height:250px;"></div>
          </el-card>
        </el-row>
        <!--地图-->
          <el-card style="margin-top:15px;">
            <el-radio v-model="radio" label="1" style="padding-left: 300px" @change="changMap(radio)">确诊人数</el-radio>
            <el-radio v-model="radio" label="2" @change="changMap(radio)">现存人数</el-radio>
            <el-link size="mini" @click="tomap()"  style="margin-left: 680px">更多信息
              <i class="el-icon-view el-icon--right"></i></el-link>
            <!--<el-laber :to="{ path: '/home' }" style="padding-left: 650px" >更多信息></el-laber>-->
            <div ref="mapbox" style="height:450px;"></div>
          </el-card>
      </el-col>
      <!-- 右边部分 -->
      <el-col :span="12">
        <!--新闻疫情-->
        <el-card style="margin-top:0px;border:0px" v-loading="loading">
          <el-carousel height="200px" direction="vertical" :autoplay="false">
            <el-carousel-item v-for="item in rumorList" :key="item.id">
              <el-scrollbar >
                <el-col :span="24" style="background-color: rgba(255,0,0,0)">
                  <div class="grid-content bg-purple-light">
                    <el-row :gutter="20">
                      <h4>{{item.title}}</h4>
                    </el-row>
                    <div>
                      <span style="color: #3099f2;margin-left:20px;">信息来源:</span>
                      <span style="color: #3099f2;margin-left:10px;">{{item.infoSource}}</span>
                      <el-link style="margin-left:10px;" type="primary"  @click="hrefClick(item.sourceUrl)">详细内容></el-link>
                    </div>
                    <div>
                      <span style="margin-left: 20px;margin-bottom:-10px;color:black;">{{item.summary}}</span>
                    </div>
                  </div>
                </el-col>
              </el-scrollbar>
            </el-carousel-item>
          </el-carousel>
        </el-card>
        <div class="grid-content bg-purple-dark" >
          <el-card class="box-card" style="margin-top:15px;margin-bottom: 10px">
            <!-- 库存饼图 -->
            <div id="bingtu" style="width: 790px;height:600px;"></div>
          </el-card>
        </div>
      </el-col>
    </el-row>
  </div>
</template>
<script>
import echarts from "echarts";
import jsonp from "jsonp";
import aplayer from "vue-aplayer";
import "echarts/map/js/china";
import CountTo from "vue-count-to";
const option = {
  title: {
    text: "疫情地图",
    link: "https://baidu.com",
    subtext: "疫情地图",
    sublink: "https://baidu.com"
  },
  grid: {
    left: '0%', right: '0%', bottom: '50%', containLabel: true
  },
  series: [
    {
      name: "累计确诊人数",
      type: "map", //告诉echarts要去渲染一个地图
      map: "china",
      label: {
        show: true, // 控制对应地区的汉字
        color: "#333",
        fontSize: 10
      },
      roam: true, //控制地图放大缩小
      zoom: 1.2, //控制地图的放大缩小
      data: [], //用来展示后台给的数据  {name:xx,value:xxx}
      /*data      控制地图板块的颜色和边框*/
      itemStyle: {
        areaColor: "#83b5e7",
        borderColor: "" //边框颜色
      },
      /*     控制鼠标滑过之后的背景色和字体颜色*/
      emphasis: {
        label: {
          color: "#fff",
          fontsize: 12
        },
        itemStyle: {
          areaColor: "#83b5e7"
        }
      }
    }
  ],
  visualMap: [
    {
      type: "piecewise",
      show: true,
      pieces: [
        //分段
        { min: 10000 },
        { min: 1000, max: 9990 },
        { min: 100, max: 999 },
        { min: 10, max: 99 },
        { min: 1, max: 9 }
      ],
      inRange: {
        symbol: "rect",
        color: ["#ffc0b1", "#e70520"]
      },
      itemWidth: 20,
      itemHeight: 10
    }
  ],
  tooltip: {
    trigger: "item" //鼠标移入后显示人数
  }
};
export default {
  components: {
    //别忘了引入组件
    aplayer: aplayer,
    CountTo
  },
  data() {
    return {
      loading: true,
      add_daily:'', daily :'',add_sustotal:'', sustotal :'',
      rumorList: [],
      queryMap: { pageSize: 100, pageNum: 1 },
      xData: [],
      yData1: [],
      yData2: [],
      myData: [],
      value: new Date(),
      userInfo: {},
      tableInfo: [],
      legendData: [], //饼图存放物资名称
      selected: {}, //存放选择的数据
      seriesData: [{ name: "", value: "" }] ,//饼图数据
      radio: '1',
      info: [],
    };
  },
  methods: {
    hrefClick(url){
      window.open(url);//打开一个新的标签页
    },
    tomap(){
      this.$router.push("/map");
    },
    async getRumorsData(page) {
      this.$http.get("https://lab.isaaclin.cn/nCoV/api/news?num=4&page="+page).then((response) => {
        this.rumorList = response.data.results;
        this.loading=false;
        //console.log(response.data.results)
      }, (error) => {})
    },
    getData() {
      jsonp(
          "https://interface.sina.cn/news/wap/fymap2020_data.d.json?_=1580892522427", //接口
          {},
          (err, data) => {
            if (!err) {
              //代表请求数据成功
              let list = data.data.list.map(item => ({
                name: item.name,
                value: item.value
              })); //从接口获取到数据后，使用map()函数，进行接口数据映射
              option.series[0].data = list;
              this.mychart.setOption(option);
              //这行代码能执行的前提是DOM已经渲染完成，只有DOM已渲染完成才能echarts初始化
              this.buildTable(data); //构建表格数据
            }
          }
      );
    },
    changMap(radio){
      if (radio==1){
        jsonp(
            "https://interface.sina.cn/news/wap/fymap2020_data.d.json?_=1580892522427", //接口
            {},
            (err, data) => {
              if (!err) {
                //代表请求数据成功
                let list = data.data.list.map(item => ({
                  name: item.name,
                  value: item.value
                })); //从接口获取到数据后，使用map()函数，进行接口数据映射
                option.series[0].name="累计确诊人数";
                option.series[0].data = list;
                this.mychart.setOption(option);
                //这行代码能执行的前提是DOM已经渲染完成，只有DOM已渲染完成才能echarts初始化
              }
            }
        );
      }
      else {
        jsonp(
            "https://interface.sina.cn/news/wap/fymap2020_data.d.json?_=1580892522427", //接口
            {},
            (err, data) => {
              if (!err) {
                //代表请求数据成功
                let list = data.data.list.map(item => ({
                  name: item.name,
                  value: item.econNum
                })); //从接口获取到数据后，使用map()函数，进行接口数据映射
                option.series[0].name="现存确诊人数";
                option.series[0].data = list;
                this.mychart.setOption(option);
                //这行代码能执行的前提是DOM已经渲染完成，只有DOM已渲染完成才能echarts初始化
              }
            }
        );
      }
    },
    buildTable(data) {
      var data = data.data;
      let data1 = {
        name: "现存确诊",
        value: data.econNum,
        yesterday: data.add_daily["addecon_new"]
      };
      let data2 = {
        name: "累计境外输入",
        value: data.jwsrNum,
        yesterday: data.add_daily["addjwsr"]
      };
      let data3 = {
        name: "现无症状",
        value: data.asymptomNum,
        yesterday: data.add_daily["addasymptom"]
      };
      let data4 = {
        name: "现存确诊重症",
        value: data.heconNum,
        yesterday: data.add_daily["addhecon_new"]
      };
      let data5 = {
        name: "累计确诊",
        value: data.gntotal,
        yesterday: data.add_daily["addcon_new"]
      };
      let data6 = {
        name: "累计死亡",
        value: data.deathtotal,
        yesterday: data.add_daily["adddeath_new"]
      };
      let data7 = {
        name: "累计治愈",
        value: data.curetotal,
        yesterday: data.add_daily["addcure_new"]
      };
      let data8 = {
        name: "现存疑似",
        value: data.sustotal,
        yesterday: data.add_daily["wjw_addsus_new"]
      };
      this.times=data.times;
      this.top10=data.jwsrTop;
      this.info.push(data1);
      this.info.push(data2);
      this.info.push(data3);
      this.info.push(data4);
      this.info.push(data5);
      this.info.push(data6);
      this.info.push(data7);
      this.info.push(data8);
      this.loading=false;
      this.daily=data.econNum;
      this.add_daily=data1.yesterday;
      this.sustotal=data8.value;
      this.add_sustotal=data8.yesterday;
    },
    /**
     * 加载库存信息
     */
    async getStockList() {
      const { data: res } = await this.$http.get("product/findProductStocks", {
        params: this.queryMap
      });
      if (res.code !== 200) {
        return this.$message.error("获取物资库存列表失败");
      } else {
        this.total = res.data.total;
        this.tableData = res.data.rows;
        this.xData = [];
        this.yData1 = [];
        this.yData2 = [];
        this.selected = {};
        var $this = this;
        //构建表格条形统计图的数据
        this.tableData.forEach(function(e) {
          $this.xData.push(e.name);
          $this.yData1.push(e.stock);
          $this.yData2.push(e.waiting);
        });
        //重新绘制表格
        this.draw();
        //饼图
        this.findAllProductStocks();
      }
    },
    /*绘制条形统计图*/
    draw() {
      var myChart = echarts.init(document.getElementById("tianxing"));
      //指定图表的配置项和数据
      var option = {
        title: {text: "库存条形图"},
        grid: {
          left: '0%',
          right: '5%',
          bottom: '0%',
          containLabel: true
        },
        toolbox: {show: true,},
        tooltip: {trigger: "axis",},
        legend: {data: ["库存量","待入库"]},//标签
        xAxis: {
          data: this.xData,
          splitLine:{show:false,},
        },
        yAxis: {},
        series: [
          {
            name: "库存量",
            stack:'使用情况',
            type: "bar",
            showBackground: false,
            data: this.yData1,
            itemStyle: {
              normal: {
                color:"#5470c6",
                //好，这里就是重头戏了，定义一个list，然后根据所以取得不同的值，这样就实现了，
                // color: function(params) {
                //   // build a color map as your need.
                //   var colorList = [
                //     '#5470c6', '#91cc75', '#fac858', '#ee6666', '#73c0de', '#3ba272', '#fc8452', '#9a60b4', '#ea7ccc',
                //     '#5470c6', '#91cc75', '#fac858', '#ee6666', '#73c0de', '#3ba272', '#fc8452', '#9a60b4', '#ea7ccc',
                //     '#5470c6', '#91cc75', '#fac858', '#ee6666', '#73c0de', '#3ba272', '#fc8452', '#9a60b4', '#ea7ccc',
                //   ];
                //   return colorList[params.dataIndex]
                // },
                //以下为是否显示，显示位置和显示格式的设置了
                // label: {
                //   show: true,
                //   position: 'top',
                //   formatter: '{b}\n{c}'
                // }
              }
            },
          },
          {
            name: "待入库",
            type: "bar",
            stack:'使用情况',
            data: this.yData2,
            itemStyle: {
              normal: {
                color:"rgba(84,112,198,0.42)",
                label: {
                  show: true,
                  position: 'top',
                  formatter: '{b}\n{c}'
                }
              }
            },
          },

        ]
      };

      // 使用刚指定的配置项和数据显示图表。
      myChart.setOption(option);
    },
    /*绘制饼图*/
    drawRound() {
      var myChart = echarts.init(document.getElementById("bingtu"));
      var option = {
        title: {
          text: "库存占比图",
          left: "left"
        },
        toolbox: {
          show: true,
          feature: {
            saveAsImage: {} // 导出图片
          }
        },
        tooltip: {
          trigger: "item",
          formatter: "{a} <br/>{b} : {c} ({d}%)"
        },
        legend: {
          left: "center",
          type: "scroll",
          //right: 10,
          //top: 20,
          bottom: 0,
          data: this.legendData,
          selected: this.selected
        },
        series: [
          {
            name: "物资名称",
            type: "pie",
            radius: "50%",
            center: ["50%", "50%"],
            data: this.seriesData,
            emphasis: {
              itemStyle: {
                shadowBlur: 10,
                shadowOffsetX: 10,
                //shadowColor: "rgba(0, 0, 0, 0.5)"
              }
            },
            itemStyle: {
              normal: {
                //好，这里就是重头戏了，定义一个list，然后根据所以取得不同的值，这样就实现了，
                color: function(params) {
                  var colorList = [
                    '#5470c6', '#91cc75', '#fac858', '#ee6666', '#73c0de', '#3ba272', '#fc8452', '#9a60b4', '#ea7ccc',
                    '#5470c6', '#91cc75', '#fac858', '#ee6666', '#73c0de', '#3ba272', '#fc8452', '#9a60b4', '#ea7ccc',
                  ];
                  return colorList[params.dataIndex]
                },
                //以下为是否显示，显示位置和显示格式的设置了
                label: {
                  show: true,
                  position: 'top',
//                             formatter: '{c}'
                  formatter: '{b}\n{c}'
                }
              }
            },
            // radius: [30, 300],
            roseType: 'radius',
          }
        ]
      };
      myChart.setOption(option);
    },
    /*物资所有的库存信息*/
    async findAllProductStocks() {
      const { data: res } = await this.$http.get("product/findAllStocks", {
        params: this.queryMap
      });
      if (res.code !== 200) {
        return this.$message.error("获取物资所有库存失败");
      } else {
        this.legendData = [];
        this.selected = {};
        this.seriesData = [{}];
        var $this = this;
        //构建饼图的数据对象
        res.data.forEach(function(e) {
          $this.legendData.push(e.name);
          $this.seriesData.push({ name: e.name, value: e.stock });
        });

        //重新绘制表格
        this.drawRound();
      }
    },
  },
  created() {
    this.getStockList();
    this.userInfo = this.$store.state.userInfo;
    var roles = this.userInfo.isAdmin ? "超级管理员" : this.userInfo.roles;
    this.tableInfo.push({
      username: this.userInfo.username,
      nickname: this.userInfo.nickname,
      department: this.userInfo.department,
      roles: roles
    });
  },
  mounted: function() {
    this.getRumorsData(1);
    this.draw();
    this.drawRound();
    this.getData(); //执行getData方法
    this.mychart = echarts.init(this.$refs.mapbox);
    this.mychart.setOption(option);
  },
};
</script>
<style scoped>
.el-carousel__item h3 {
  color: #fff;
  font-size: 14px;
  opacity: 0.75;
  line-height: 200px;
  margin: 0;
}
.el-carousel__item:nth-child(2n) {
  background-color: #fff;
  background-size: 100% 100%;
}
.el-carousel__item:nth-child(2n + 1) {
  background-color: #ffffff;
  background-size: 100% 100%;
}

.card-data__body {
  padding: 0px;
}
</style>
<style>

</style>