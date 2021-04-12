<template>
  <div>
    <!-- 面包导航 -->
    <el-breadcrumb separator="/" style="padding-left:10px;padding-bottom:10px;font-size:12px;">
      <el-breadcrumb-item :to="{ path: '/home' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>疫情信息</el-breadcrumb-item>
      <el-breadcrumb-item>疫情新闻</el-breadcrumb-item>
    </el-breadcrumb>
    <!--        列表部分-->
    <el-card style="min-height: 850px;">
      <el-button-group>
        <el-button size="mini" type="primary" @click="firstPage" :disabled="this.page==1">首页</el-button>
        <el-button
                size="mini" type="primary" icon="el-icon-arrow-left" @click="prePage" :disabled="this.page==1">上一页</el-button>
        <el-button size="mini" type="primary" @click="nextPage" :disabled="this.rumorList.length==0">下一页
          <i class="el-icon-arrow-right el-icon--right"></i>
        </el-button>
      </el-button-group>
      <el-row :gutter="20">
        <el-col style="margin-top: 10px;" v-for="item in rumorList" :key="item.id" :span="24" v-loading="loading">
          <div class="grid-content bg-purple">
            <el-row>
              <el-col :span="20" style="background-color: rgba(200,200,169,0.11)">
                <div class="grid-content bg-purple-light">
                    <h3 style="margin: 0px;;cursor: pointer;" >{{item.title}}</h3>
                  <div style="margin-top:20px;">
                    <span style="font-size: 11px;margin-left:20px;color:#303030;">{{item.summary}}</span>
                  </div>
                  <div>
                    <el-col :span="13">
                      <span style="font-size: 11px;color: #3099f2;margin-left:20px;">信息来源:</span>
                      <span style="font-size: 11px;color: #3099f2;margin-left:10px;">{{item.infoSource}}</span>
                      <el-link style="margin-left:10px;" type="primary"  @click="hrefClick(item.sourceUrl)">详细内容></el-link>

                    </el-col>
                  </div>
                </div>
              </el-col>
            </el-row>
          </div>
        </el-col>
      </el-row>
    </el-card>
  </div>
</template>
<script>
  export default {
    data() {
      return {
        loading:true,
        count: 0,
        page: 1,
        rumorList: []
      };
    },
    methods: {
      hrefClick(url){
        //window.location.href="https://www.baidu.com/";//当前标签页
        window.open(url);//打开一个新的标签页
      },
      //跳转到详情
      // detail(id) {
      //   this.$router.push({
      //     path: "/rumors/detail",
      //     query: {
      //       id: id
      //     }
      //   });
      // },
      /**加载数据*/
      //加载系统日志列表
      async getData(page) {
        this.$http.get("https://lab.isaaclin.cn/nCoV/api/news?num=10&page="+page).then((response) => {
          this.rumorList = response.data.results;
          this.loading=false;
          console.log(response.data.results)
        }, (error) => {
          // console.log('error:', error)
        })
      },
      //下一页
      nextPage() {
        this.page = this.page + 1;
        this.getData(this.page);
      },
      prePage() {
        this.page = this.page - 1;
        this.getData(this.page);
      },
      firstPage() {
        this.page = 1;
        this.getData(this.page);
      }
    },
    mounted() {
      //template挂载到页面时调用
      this.getData(1); //执行getData方法
    }
  };
</script>

