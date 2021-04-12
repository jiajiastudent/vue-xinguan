<template>
  <div id="qq">
    <!-- 面包导航 -->
    <el-breadcrumb separator="/" style="padding-left:10px;padding-bottom:10px;font-size:12px;">
      <el-breadcrumb-item :to="{ path: '/home' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>其他管理</el-breadcrumb-item>
      <el-breadcrumb-item>QQ群</el-breadcrumb-item>
    </el-breadcrumb>

    <el-card style="min-height:500px;">
      <el-row :gutter="10">
        <el-col :span="6">
          <div class="grid-content bg-purple">
            <h1>QQ交流群： 830790908</h1>
            <el-image
              style="width:250px;"
              :src="'https://www.zykcoderman.xyz/resources/res/images/weixin.png'"
            >
              <div slot="placeholder" class="image-slot">
                加载中
                <span class="dot">...</span>
              </div>
            </el-image>
          </div>
        </el-col>
        <el-col :span="6">
          <div class="grid-content bg-purple">
            <h1>打赏作者一杯 coffee ~</h1>
            <el-image
              style="width:180px;"
              :src="'http://www.zykhome.club:8080/upload/2020/04/weixinpay-aaacdfd2d1ea4e6c906345321d6861bb.png'"
            >
              <div slot="placeholder" class="image-slot">
                加载中
                <span class="dot">...</span>
              </div>
            </el-image>
          </div>
        </el-col>
        <el-col :span="12">
          <el-table border :data="tableData" style="width: 100%">
            <el-table-column prop="name" label="捐赠人" width="130" :show-overflow-tooltip="true"></el-table-column>
            <el-table-column prop="money" label="捐赠金额"></el-table-column>
            <el-table-column prop="time" label="捐赠时间" width="120"></el-table-column>
            <el-table-column prop="remark" label="留言" :show-overflow-tooltip="true"></el-table-column>
            <el-table-column prop="type" label="捐赠方式"></el-table-column>
          </el-table>
        </el-col>
      </el-row>
    </el-card>
  </div>
</template>

<script>
export default {
  data() {
    return {
      tableData: [
          {
          name: "沉默的**",
          money: "10.00 RMB",
          time: "2020/4/20",
          remark: "不错不错，支持站长~",
          type: "微信"
        },
         {
          name: "努力努力再**",
          money: "1.00 RMB",
          time: "2020/4/28",
          remark: "支持一下",
          type: "支付宝"
        },
          {
          name: "与你常在*",
          money: "100.00 RMB",
          time: "2020/5/20",
          remark: "请作者喝杯咖啡",
          type: "微信"
        },
         {
          name: "KevinZhang",
          money: "8.80 RMB",
          time: "2020/5/28",
          remark: "赞一个",
          type: "支付宝"
        },
       
         {
          name: "*睿",
          money: "10.00 RMB",
          time: "2020/6/14",
          remark: "行走的小白",
          type: "微信"
        },
        {
          name: "-198***@qq.com",
          money: "0.01 RMB",
          time: "2020/6/29",
          remark: "新冠物资系统写的草鸡好，学习了",
          type: "支付宝"
        }
      ]
    };
  }
};
</script>
