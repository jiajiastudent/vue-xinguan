<template>
    <div class="outStocks">
        <!-- 面包导航 -->
        <el-breadcrumb separator="/" style="padding-left:10px;padding-bottom:10px;font-size:12px;">
            <el-breadcrumb-item :to="{ path: '/home' }">首页</el-breadcrumb-item>
            <el-breadcrumb-item>物资管理</el-breadcrumb-item>
            <el-breadcrumb-item>出库记录</el-breadcrumb-item>
        </el-breadcrumb>
        <!-- 卡片部分-->
          <el-card style="margin-bottom:15px;" >
            <label style="font-size:20px;font-weight: bold">可用车辆信息&nbsp&nbsp</label>
            <el-col :span="24">
              <label size="medium"  style="margin-right:10px;color:gray;font-size:18px;margin-bottom:10px" >库存数:{{carTotal}}/{{carTotalALL}}辆</label>
            <el-tag size="medium"  type="success" v-for="item in carTypeTable"
                    style="margin-right:10px;font-size:18px;margin-bottom:10px" v-if="item.type==1">小型卡车(载重{{item.weight}}kg):{{ item.num}}辆</el-tag>
            <el-tag size="medium"  type="success" v-for="item in carTypeTable"
                    style="margin-right:10px;font-size:18px;margin-bottom:10px" v-if="item.type==2">中型卡车(载重{{item.weight}}kg):{{ item.num}}辆</el-tag>
            <el-tag size="medium"  type="success" v-for="item in carTypeTable"
                    style="margin-right:10px;font-size:18px;margin-bottom:10px" v-if="item.type==3">大型卡车(载重{{item.weight}}kg):{{ item.num}}辆</el-tag>
          </el-col>
        </el-card>
      <el-card style="margin-bottom:15px;" >
        <label style="font-size:20px;font-weight: bold">物资库存信息</label>
        <el-col :span="24">
          <label size="medium"  style="margin-right:10px;color:gray;font-size:18px;margin-bottom:10px" >需求量/库存</label>
          <el-tag size="medium"  type="success" v-for="item in alldetailTable" v-if="alldetailvisible==true"
                  style="margin-right:10px;font-size:18px;margin-bottom:10px">{{ item.name}}：{{item.need}}/{{ item.stock}}</el-tag>
          <el-tag size="medium"  type="success"  v-if="alldetailvisible==false"
                  style="margin-right:10px;font-size:18px;margin-bottom:10px">暂无需要匹配的物资</el-tag>
        </el-col>
      </el-card>
      <el-card style="margin-bottom:15px;" v-if="consumerTableVisable">
        <el-col :span="24">
          <label style="font-size:20px;font-weight: bold">自动规划路线</label>
<!--          <el-tag size="medium"  type="warning" style="margin-right:10px;font-size:18px;margin-bottom:10px" v-if="consumerTableVisable">自动规划的路线:</el-tag>-->
          <div  v-for="item in consumerByAuto" style="margin-right:2px;font-size:18px;margin-bottom:10px" v-if="consumerTableVisable">

            <el-tag style="margin-right:2px;font-size:18px;margin-bottom:10px;margin-left: 15px;margin-right: 15px">
              车牌号：{{item.carNum}}
            </el-tag>
            <el-tag  v-if="item.type==3"style="margin-right:2px;font-size:18px;margin-bottom:10px">大型卡车</el-tag>
            <el-tag  v-if="item.type==2"style="margin-right:2px;font-size:18px;margin-bottom:10px">中型卡车</el-tag>
            <el-tag  v-if="item.type==1"style="margin-right:2px;font-size:18px;margin-bottom:10px">小型卡车</el-tag>
            <el-tag type="info"style="margin-right:2px;font-size:18px;margin-bottom:10px;margin-left: 15px" >全程:{{item.distance}}km 大约需要{{item.hour}}h {{item.min}}min</el-tag>
            <el-dropdown trigger="click">
              <span style="margin-left:10px;font-size:18px;margin-bottom:10px">查看单号<el-badge class="mark" :value="item.outNumList.length" /><i class="el-icon-caret-bottom el-icon--right"></i></span>
              <el-dropdown-menu slot="dropdown" style="border-left: 80px">
                <el-dropdown-item  v-for="i in item.outNumList" @click="changeOutNum(i)">{{i}}</el-dropdown-item>
              </el-dropdown-menu>
            </el-dropdown>



<!--            <el-select v-model="value" placeholder="选择单号">-->
<!--              <el-option-->
<!--                  v-for="i in item.outNumList":label="i">-->
<!--              </el-option>-->
<!--            </el-select>-->
            <div>
              <label style="margin-left: 15px">路线：</label>
              <el-tag  type="danger"v-for="i in item.consumerList"style="margin-right:2px;font-size:18px;margin-bottom:10px">
                <label>{{ i.name}}</label>
                <label>-></label>
              </el-tag>
              <el-tag   type="danger"v-if="consumerTableVisable"style="margin-right:2px;font-size:18px;margin-bottom:10px">仓库</el-tag>
              <el-link type="success" size="medium" style="left:10px" @click="ClickGetMap(item.consumerList)">查看地图信息-></el-link>
            </div>
          </div>
        </el-col>
      </el-card>

        <el-card>
          <label style="font-size:20px;font-weight: bold;margin-bottom: 10px">出库记录</label>
        <!--搜索部分-->
            <el-form size="mini" :inline="true" :model="queryMap" class="demo-form-inline">
                <el-form-item label="发放单号">
                    <el-input v-model="queryMap.outNum" placeholder="发放单号"></el-input>
                </el-form-item>
                <el-form-item label="发放类型">
                    <el-select v-model="queryMap.type" placeholder="发放类型">
                        <el-option label="全部类型" value=""></el-option>
                        <el-option label="其他领取" value="0"></el-option>
                        <el-option label="个人领取" value="1"></el-option>
                        <el-option label="小区领取" value="2"></el-option>
                        <el-option label="医院领取" value="3"></el-option>
                        <el-option label="政府领取" value="4"></el-option>
                    </el-select>
                </el-form-item>
                <el-form-item>
                    <el-select    v-model="queryMap.status" placeholder="请选择状态">
                        <el-option label="已发放" :value="0"></el-option>
                        <el-option label="发放中" :value="1"></el-option>
                        <el-option label="待审核" :value="2"></el-option>
                    </el-select>
                </el-form-item>
                <el-form-item>
                    <el-button type="primary" @click="search">查询</el-button>
                    <el-button  @click="reset">重置</el-button>
                </el-form-item>
                <el-form-item>
                    <router-link to="/OutStocks/publishStocks">
                        <el-button type="success" >发放</el-button>
                    </router-link>
                </el-form-item>
                <el-form-item>
                  <el-button  type="primary" @click="beignCreate" v-if="isAdmin">开始自动匹配</el-button>
                </el-form-item>
            </el-form>

            <!--<el-tag size="medium"  type="success"-->
                    <!--style="margin-left:600px;font-size:18px;margin-bottom:10px"v-if="tableDataStatus">全部物资已发货</el-tag>-->
<!--            数据表格-->

                <el-table size="mini" border :data="tableData" style="width: 100%;height: 450px; margin-bottom:10px" >
                    <el-table-column label="#" prop="id" width="50"></el-table-column>
                    <el-table-column  prop="outNum" :show-overflow-tooltip='true' label="发放单号" width="180"></el-table-column>
                    <el-table-column prop="type" label="发放类型" width="100">
                        <template slot-scope="scope">
                            <el-tag  effect="plain" size="mini"  type="success" v-if="scope.row.type===0">其他领取</el-tag>
                            <el-tag  effect="plain" size="mini" type="danger"  v-else-if="scope.row.type===1">个人领取</el-tag>
                            <el-tag  effect="plain" size="mini"  type="warning"  v-else-if="scope.row.type===2">小区领取</el-tag>
                            <el-tag  effect="plain" size="mini"  type="info"  v-else-if="scope.row.type===3">医院领取</el-tag>
                            <el-tag  effect="plain" size="mini"   v-else-if="scope.row.type===4">政府领取</el-tag>
                        </template>
                    </el-table-column>
                    <el-table-column prop="priority" label="紧急程度" width="180">
                        <template slot-scope="scope">
                            <el-rate :disabled="true" v-model="scope.row.priority" show-text :texts="['不急','常规','紧急','特急','超急']">
                            </el-rate>
                        </template>
                    </el-table-column>
                    <el-table-column prop="name" label="发放地点" width="150"></el-table-column>
                    <el-table-column prop="productNumber" label="总数" width="80"></el-table-column>
                    <el-table-column prop="weight" label="重量（kg）" width="100"></el-table-column>
                    <el-table-column prop="phone" label="联系方式" width="120"></el-table-column>
                    <el-table-column prop="status" label="状态" width="100">
                        <template slot-scope="scope">
                            <el-tag size="mini" type="danger" effect="plain" v-if="scope.row.status==1">发放中</el-tag>
                            <el-tag size="mini" effect="plain" v-if="scope.row.status==0">已放</el-tag>
                            <el-tag size="mini" effect="plain" type="warning" v-if="scope.row.status==2">审核中</el-tag>
                        </template>
                    </el-table-column>

                    <el-table-column prop="operator" label="操作员" width="150"></el-table-column>

                    <el-table-column prop="createTime" label="发放时间" width="200px;"></el-table-column>
                    <el-table-column label="操作"  width="200" >
                        <template slot-scope="scope">
                            <el-button icon="el-icon-view" type="text" size="small" @click="detail(scope.row.id)">明细</el-button>
                            <!--给操作员使用的按钮-->
<!--                            <span v-if="scope.row.status==0">-->
<!--                              <el-button icon="el-icon-s-operation" type="text" size="small" @click="remove(scope.row.id)">发放中</el-button>-->
<!--                            </span>-->
                            <!--   给操作员使用的按钮(回收站)-->
                            <span v-if="scope.row.status==1&isAdmin">
                              <el-button icon="el-icon-s-operation" type="text" size="small" @click="back(scope.row.id)">完成发放</el-button>
<!--                              <el-button icon="el-icon-delete" type="text" size="small" @click="del(scope.row.id)">删除</el-button>-->
                            </span>
                            <!-- 给审核员使用的按钮-->
                            <span v-if="scope.row.status==2&isAdmin">
                              <el-button icon="el-icon-refresh" type="text" size="small" @click="publish(scope.row.id)">通过</el-button>
<!--                              <el-button icon="el-icon-delete" type="text" size="small" @click="del(scope.row.id)">删除</el-button>-->
                            </span>
                        </template>
                    </el-table-column>
                </el-table>
<!--                表格分页-->
                <el-pagination style="margin-top:20px;" background @size-change="handleSizeChange" @current-change="handleCurrentChange"
                        :current-page="queryMap.pageNum" :page-sizes="[10, 20, 30, 40]" :page-size="queryMap.pageSize" layout="total, sizes, prev, pager, next, jumper"
                        :total="total"></el-pagination>
            <!-- 发放明细 -->
            <el-dialog title="发放明细" :visible.sync="dialogVisible" @close="closeDetail" width="60%">
                <!-- 来源信息-->
                <el-card class="box-card" style="margin-bottom: 10px;">
                    <div  class="text item">
                        <el-row :gutter="20">
                            <el-col :span="6">
                                <div class="grid-content bg-purple">
                                    <span style="font-size: 11px;color: #303030;">省区市:</span> &nbsp;<el-tag size="mini" >{{consumer.address}}</el-tag>
                                </div>
                            </el-col>
                            <el-col :span="6">
                                <div class="grid-content bg-purple">
                                    <span style="font-size: 11px;color: #303030;">具体位置:</span> &nbsp;<el-tag size="mini" >{{consumer.name}}</el-tag>
                                </div>
                            </el-col>
                            <el-col :span="6">
                                <div class="grid-content bg-purple">
                                    <span style="font-size: 11px;color: #303030;">联系人 :</span> &nbsp;<el-tag size="mini"  >{{consumer.contact}}</el-tag>
                                </div>
                            </el-col>
                            <el-col :span="6">
                                <div class="grid-content bg-purple">
                                    <span style="font-size: 11px;color: #303030;">电话 : </span>&nbsp;<el-tag size="mini" >{{consumer.phone}}</el-tag>
                                </div>
                            </el-col>

                        </el-row>
                    </div>
                </el-card>
                <!--步骤条-->
                <el-steps simple v-if="pStatus==0"  style="margin-left: 10px;margin-bottom: 5px;" :space="200" :active="3" finish-status="success">
                    <el-step title="提交发放单" ></el-step>
                    <el-step title="审核发放单"></el-step>
                    <el-step title="成功发放"></el-step>
                </el-steps>
                <el-steps simple v-if="pStatus==2"  style="margin-left: 10px;margin-bottom: 5px;" :space="200" :active="2" finish-status="success">
                    <el-step title="提交发放单" ></el-step>
                    <el-step title="审核发放单"></el-step>
                    <el-step title="成功发放"></el-step>
                </el-steps>
                <span>
          <template>
            <el-table height="260" max-height="350" border :data="detailTable" style="width: 100%">
              <el-table-column prop="name" label="名称"></el-table-column>
              <el-table-column :show-overflow-tooltip="true" prop="pnum" label="商品编号"></el-table-column>
               <el-table-column prop="model" label="规格"></el-table-column>
<!--              <el-table-column-->
<!--                      prop="imageUrl"-->
<!--                      label="图片"-->
<!--                      align="center"-->
<!--                      width="150px"-->
<!--                      padding="0px"-->
<!--              >-->
<!--                <template slot-scope="scope">-->
<!--                  <img-->
<!--                          :src="'https://www.zykhome.club/'+scope.row.imageUrl"-->
<!--                          alt-->
<!--                          style="width: 30px;height:30px"-->
<!--                  />-->
<!--                </template>-->
<!--              </el-table-column>-->
               <el-table-column prop="count" label="数量"></el-table-column>
                <el-table-column prop="unit" label="单位"></el-table-column>
                <el-table-column prop="weight" label="重量(kg)"></el-table-column>

            </el-table>
              <!-- 明细分页-->
        <el-pagination style="margin-top:20px;" background @current-change="handleDetailSizeChange" :current-page="this.pageNum"
                :page-size="3" layout="prev, pager, next,total" :total="this.detailTotal">
        </el-pagination>
          </template>
        </span>
            </el-dialog>
            <!-- 地圖信息弹出框 -->
            <el-dialog title="地图信息" :visible.sync="mapDialogVisible" width="80%" @close="closeMapDialog">
                <baidu-map class="bmView" :scroll-wheel-zoom="true" :center="center" :zoom="zoom" ak="FV4ZcOesQ6tpzr8i0R0ePq0Q" >
                    <bm-local-search :keyword="addressKeyword" :auto-viewport="true" style="display: none"></bm-local-search>
                    <bm-view style="width: 100%; height:800px; flex: 1" ></bm-view>
                  <bm-driving
                      :start="{lng: 116.124343, lat: 39.964918}"
                      :end="{lng: 116.124343, lat: 39.964918}"
                      :auto-viewport="true"
                      :waypoints=waypoints></bm-driving>
<!--                    <bm-driving :start="start" :end="start"-->
<!--                                :waypoints="waypoints"></bm-driving>-->
<!--                  <bm-polyline :path="waypoints" stroke-color="blue" :stroke-opacity="1" :stroke-weight="4" :editing="false">-->
<!--                  </bm-polyline>-->
                </baidu-map>
                <span slot="footer" class="dialog-footer">
                    <el-button @click="mapDialogVisible = false">取 消</el-button>
                </span>
            </el-dialog>
        </el-card>
    </div>
</template>

<script>
    export default {
        data() {
            return {
                isAdmin:false,
                value: '',
                // waypoints:[ {lng: 113.33, lat: 37.84},{lng: 112.53, lat: 23.87},{lng: 121.53, lat: 30.87} ],
                alldetailvisible:false,
                waypoints:[],
                start: {lng: 116.124343, lat: 39.964918},
                end: {lng: 116.124343, lat: 39.964918},
                zoom: 12.8,
                addressKeyword: "",
                center:"北京",
                mapList:[],
                mapDialogVisible:false,
                tableDataStatus:false,
                consumerTableVisable:false,
                list:[1,2,3,4,5,6],
                alldetailTable:[],
                consumer:{},
                consumerByAuto:[],
                beignCreateTable:{},
                detailTotal:0,
                detailTable:[],
                dialogVisible:false,
                pageNum:1,
                total:0,
                queryMap:{pageNum:1,pageSize:10, status: 2},
                queryCarMap:{pageNum:1,pageSize:1000,status:0},
                queryCarMapALL:{pageNum:1,pageSize:1000},
                carTypeTable:[],
                carData:[],
                carTypeTotal:0,
                carTotal:0,
                carTotalALL:0,
                tableData:[],
                pStatus:'',//步骤flag
            }
        },
        methods:{
            //获取地图信息
            ClickGetMap(consumerList) {
                this.waypoints=[];
                /*this.waypoints.splice(0, this.waypoints.length);*/
              console.log(consumerList)
                // let list = consumerList.map(item => ({
                //     lng: item.lng,
                //     lat: item.lat
                // }));
                // for (let i = 1; i < list.length; i++) {
                //     this.waypoints.push(list[i]);
                //     console.log((list[i]))
                // }
                //
              for (let item of consumerList){
                this.waypoints.push({lng:item.lng,lat:item.lat});
              }
                console.log("way:")
                console.log(this.waypoints)
                // console.log("list:")
                // console.log(list)
                this.mapDialogVisible=true;
            },
            //关闭地圖信息弹出框
            closeMapDialog() {
              this.mapDialogVisible=false;
            },
                async getCarListType(){
                const { data: res } = await this.$http.get(
                    "car/findAllType",
                );
                if (res.code !== 200) {
                    return this.$message.error("获取car 列表失败");
                }else {
                    this.carTypeTable = res.data.rows;
                    this.carTypeTotal=res.data.total;
                }
            },
            async getCarListALL(){
                const { data: res } = await this.$http.get(
                    "car/findAll",
                    {params: this.queryCarMapALL}
                );
                if (res.code !== 200) {
                    return this.$message.error("获取car列表失败");
                }else {
                    this.carTotalALL = res.data.total;
                }
            },
            async getCarList(){
                const { data: res } = await this.$http.get(
                    "car/findAll",
                    {params: this.queryCarMap}
                );
                if (res.code !== 200) {
                    return this.$message.error("获取car列表失败");
                }else {
                    this.carTotal = res.data.total;
                    this.carData=res.data.rows;
                }
                //console.log("total:"+this.carTotal);
                //console.log(this.carData);
            },
            //物资发放审核
            async publish(id){
                const { data: res } = await this.$http.put("outStock/publish/"+id);
                if (res.code !== 200) {
                    return this.$message.error("审核失败:"+res.msg);
                } else {
                    this.loadTableData();
                    this.alldetail();
                    return this.$message.success("发放已审核通过");
                }
            },
            //关闭明细
            closeDetail(){
                this.pageNum=1;
            },
            //改变页码
            handleDetailSizeChange(newSize) {
                this.pageNum = newSize;
                this.detail(this.detailId);
            },
            /**
             * 查看发放单明细
             */
            async detail(id) {
                this.detailId=id;
                const {data: res} = await this.$http.get("outStock/detail/" + id+"?pageNum="+this.pageNum);
                if (res.code !== 200) {
                    this.$message.error("获取明细失败:" + res.msg);

                } else {
                    this.detailTable = res.data.itemVOS;
                    this.detailTotal = res.data.total;
                    this.consumer=res.data.consumerVO;
                    this.pStatus=res.data.status;
                    this.dialogVisible = true;
                }

            },
            //开始自动匹配算法
            async beignCreate(){
                const {data: res} = await this.$http.get("outStock/beignCreate" );
                if (res.code !== 200) {
                    this.$message.error("获取失败:" + res.msg);
                } else {
                    this.getCarList();
                    this.getCarListType();
                    this.loadTableData();
                    this.beignCreateTable = res.data;
                    this.consumerByAuto=res.data.carDistanceVOList;
                    this.consumerTableVisable=true;
                    if(this.tableData.length==0){
                        this.tableDataStatus=true;
                    }else {
                        this.tableDataStatus=false;
                    }
                    if(res.data.distance!=0){
                        if(res.data.hour==0){
                            this.$message.success("路线规划完成。全程："+res.data.distance+"km。大约需要"+res.data.min+"分钟");
                        }else{
                            this.$message.success("路线规划完成。全程："+res.data.distance+"km。大约需要"+res.data.hour+"小时"+res.data.min+"分钟");
                        }
                    }
                    if(res.data.status==1){
                        this.$notify({
                            title: "提醒",
                            message: "车辆不够请稍后分配",
                            type: "success"
                        });
                        //this.$message.success("车辆不够请稍后分配");
                    }
                    console.log(res.data)
                }
            },
            //alldetail获取所有的物资需求细节
            async alldetail() {
                const {data: res} = await this.$http.get("outStock/alldetail" );
                if (res.code !== 200) {
                    this.$message.error("获取明细失败:" + res.msg);
                } else {

                    this.alldetailTable = res.data;
                    if(this.alldetailTable.length!=0){
                      this.alldetailvisible=true;
                    }
                    //console.log(res.data)
                }
            },
            /*从回收站恢复*/
            async back(id){
                const { data: res } = await this.$http.put("outStock/back/"+id);
                if (res.code !== 200) {
                    return this.$message.error("加入已完成失败:"+res.msg);
                } else {
                    this.loadTableData();
                    this.alldetail();
                    return this.$message.success("已加入完成发放");
                }
            },
            /* 移除发放中*/
            async remove(id) {
                const {data: res} = await this.$http.put("outStock/remove/" + id);
                if (res.code !== 200) {
                    return this.$message.error("移入发放中失败:" + res.msg);
                } else {
                    this.loadTableData();
                    this.alldetail();
                    return this.$message.success("移入发放中成功");
                }
            },
            /* 重置查询表单*/
            reset(){
                this.queryMap={pageNum:1,pageSize:7 ,status: 0,};
            },
            /*加载表格数据             */
            async loadTableData() {
                const {data: res} = await this.$http.get("outStock/findOutStockList", {
                    params: this.queryMap
                });
                if (res.code !== 200) {
                    return this.$message.error("获取列表失败");
                } else {
                    if(this.tableData.length==0){
                        this.tableDataStatus=true;
                    }else {
                        this.tableDataStatus=false;

                    }
                    this.total = res.data.total;
                    this.tableData = res.data.rows;
                }
            },
            /*改变页码*/
            handleSizeChange(newSize) {
                this.queryMap.pageSize = newSize;
                this.loadTableData();
            },
            /*点击分页        */
            handleCurrentChange(current) {
                this.queryMap.pageNum = current;
                this.loadTableData();
            },
            //搜索
            search() {
                this.queryMap.pageNum = 1;
                this.loadTableData();
            },
            //搜索
            changeOutNum(outNum) {
              console.log(begin)
              this.queryMap.pageNum = 1;
              this.queryMap.outNum = outNum;
              this.queryMap.status = 0;
              this.loadTableData();
              console.log(end)

            },
            /*删除明细*/
            async del(id) {
                const {data: res} = await this.$http.get("outStock/delete/" + id);
                if (res.code !== 200) {
                    return this.$message.error("删除失败:" + res.msg);
                } else {
                    this.loadTableData();
                    this.alldetail();
                    return this.$message.success("删除发放单记录成功");
                }
            },

        },
        created() {
          this.isAdmin = this.$store.state.userInfo.isAdmin;
            this.getCarList();
            this.getCarListALL();
            this.getCarListType();
            this.loadTableData();
            this.alldetail();
        }
    }
</script>
