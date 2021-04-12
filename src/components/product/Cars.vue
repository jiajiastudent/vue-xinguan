<template>
    <div id="departments">
        <!-- 面包导航 -->
        <el-breadcrumb separator="/" style="padding-left:10px;padding-bottom:10px;font-size:12px;">
            <el-breadcrumb-item :to="{ path: '/home' }">首页</el-breadcrumb-item>
            <el-breadcrumb-item>车辆管理</el-breadcrumb-item>
        </el-breadcrumb>
        <!-- 右侧卡片区域 -->
        <!-- 用户列表卡片区 -->
        <el-card class="box-card">
            <el-row :gutter="20">
                <el-col :span="8">
                    <el-input size="small" clearable v-model="queryMap.carNum" placeholder="请输入车牌查询" @clear="search" class="input-with-select">
                        <el-button slot="append" icon="el-icon-search" @click="search"></el-button>
                    </el-input>
                </el-col>
                <el-col :span="2">
                    <el-button size="small" v-hasPermission="'car:add'" type="success" icon="el-icon-circle-plus-outline" @click="addDialogVisible=true">添加</el-button>
                </el-col>
            </el-row>
            <!-- 表格区域 -->
            <template>
                <el-table border size="small" v-loading="loading" stripe :data="carData" style="width: 100%;margin-top:20px;" height="720">
                    <el-table-column prop="id" type="index" label="ID" width="50"></el-table-column>
                    <el-table-column prop="carNum" label="车牌号" ></el-table-column>
                    <el-table-column prop="type" label="类型" >
                        <template slot-scope="scope">
                            <p v-if="scope.row.type==1">小型卡车</p><p v-else-if="scope.row.type==2">中型卡车</p><p v-else>大型卡车</p>
                        </template>
                    </el-table-column>
                    <el-table-column prop="weight" label="载重量" sortable >
                        <template slot-scope="scope">
                            <p v-text="scope.row.weight+'kg'" ></p>
                        </template>
                    </el-table-column>
                    <!--<el-table-column prop="status" label="改变状态" width="100">-->
                        <!--<template slot-scope="scope">-->
                            <!--<el-switch v-model="scope.row.status" @change="changStatus(scope.row)"></el-switch>-->
                        <!--</template>-->
                    <!--</el-table-column>-->
                    <el-table-column prop="status" label="状态" >
                        <template slot-scope="scope">
                                <el-switch   :active-value="1" :inactive-value="0" v-model="scope.row.status" @change="changStatus(scope.row)"></el-switch>
                        {{scope.row.status==0?'未使用':'使用中'}}
            </template>
                    </el-table-column>
                    <el-table-column label="操作">
                        <template slot-scope="scope">
                            <el-button v-hasPermission="'car:edit'" type="text" size="small" icon="el-icon-edit" @click="edit(scope.row.id)">编辑</el-button>
                            <el-button v-hasPermission="'car:delete'" type="text" size="small" icon="el-icon-delete" @click="del(scope.row.id)">删除</el-button>
                        </template>
                    </el-table-column>
                </el-table>
            </template>
            <!-- 分页 -->
            <el-pagination style="margin-top:10px;" background @size-change="handleSizeChange" @current-change="handleCurrentChange" :current-page="this.queryMap.pageNum"
                    :page-sizes="[7, 10, 15, 20]" :page-size="this.queryMap.pageSize" layout="total, sizes, prev, pager, next, jumper" :total="total"></el-pagination>
            <!-- 部门别添加弹出框 -->
            <el-dialog title="添加车辆" :visible.sync="addDialogVisible" width="50%" @close="closeAddDialog">
        <span>
          <el-form :model="addRuleForm" :rules="addRules" ref="addRuleFormRef" label-width="100px" class="demo-ruleForm">
            <el-col :span="20">
                <el-form-item label="车牌号" prop="carNum" >
                  <el-input v-model="addRuleForm.carNum"></el-input>
                </el-form-item>
            </el-col>
            <el-col :span="12">
                <el-form-item label="车辆类型" prop="type">
                    <el-select v-model="addRuleForm.type" placeholder="请选择车辆类型">
                          <el-option v-for="type in types" :key="type.value" :label="type.label" :value="type.value"></el-option>
                        </el-select>
                </el-form-item>
            </el-col>
            <el-col :span="12">
              <el-form-item label="车辆载重" prop="weight">
                <el-select v-model="addRuleForm.weight" placeholder="请选择车辆重量">
                      <el-option v-for="weight in weights" :key="weight.value" :label="weight.label" :value="weight.value"></el-option>
                    </el-select>
                </el-form-item>
            </el-col>
          </el-form>
        </span>
                <span slot="footer" class="dialog-footer">
          <el-button @click="addDialogVisible = false">取 消</el-button>
          <el-button type="primary" @click="add" >确 定</el-button>
        </span>
            </el-dialog>
            <!-- 部门别编辑弹出框 -->
            <el-dialog title="更新部门" :visible.sync="editDialogVisible" width="50%" @close="closeEditDialog">
        <span>
          <el-form :model="editRuleForm" :rules="addRules" ref="editRuleFormRef" label-width="100px" class="demo-ruleForm">
            <el-col :span="20">
                <el-form-item label="车牌号" prop="carNum" >
                  <el-input v-model="editRuleForm.carNum"></el-input>
                </el-form-item>
            </el-col>
            <el-col :span="12">
                <el-form-item label="车辆类型" prop="type">
                    <el-select v-model="editRuleForm.type">
                          <el-option v-for="type in types" :key="type.value" :label="type.label" :value="type.value"></el-option>
                        </el-select>
                </el-form-item>
            </el-col>
            <el-col :span="12">
              <el-form-item label="车辆载重" prop="weight">
                <el-select v-model="editRuleForm.weight">
                      <el-option v-for="weight in weights" :key="weight.value" :label="weight.label" :value="weight.value"></el-option>
                    </el-select>
                </el-form-item>
            </el-col>
              <el-col :span="12">
                <el-form-item prop="status" label="改变状态" width="100">
                    <template slot-scope="scope">
                        <!--<el-switch v-model="editRuleForm.status" @change="changStatus(editRuleForm.status)"></el-switch>-->
                        <el-switch v-model="editRuleForm.status" ></el-switch>

                    </template>
                </el-form-item>
            </el-col>
          </el-form>
        </span>
                <span slot="footer" class="dialog-footer">
          <el-button @click="editDialogVisible = false">取 消</el-button>
          <el-button type="primary" @click="update" :disabled="btnDisabled" :loading="btnLoading">确 定</el-button>
        </span>
            </el-dialog>
        </el-card>
    </div>
</template>

<script>
    import axios from "axios";
    export default {
        data() {
            return {
                types: [{value: 1, label: '小型卡车'}, {value: 2, label: '中型卡车'}, {value: 3, label: '大型卡车'}],
                weights: [{value: 20, label: '20kg'}, {value: 40, label: '40kg'}, {value: 60, label: '60kg'}],
                carData: [], //表格数据
                btnLoading: false,
                btnDisabled: false,
                loading: true,
                editDialogVisible: false,
                addDialogVisible: false, //添加弹框是否显示
                total: 0, //总共多少条数据
                queryMap: { pageNum: 1, pageSize: 10, carNum: "" }, //查询对象
                addRuleForm: {carNum:"",type:"",weight:""}, //添加表单数据
                editRuleForm: {}, //修改表单数据;
                addRules: {carNum: [{ message: "请输入车牌号", trigger: "blur" }],} //添加验证
            };
        },
        methods: {
             //搜索部门
            search() {
                this.queryMap.pageNum = 1;
                this.getCarList();
            },
            //改变使用状态
             //删除部门
            del: async function (id) {
                let res = await this.$confirm(
                    "此操作将永久删除, 是否继续?",
                    "提示",
                    {
                        confirmButtonText: "确定",
                        cancelButtonText: "取消",
                        type: "warning"
                    }
                ).catch(() => {
                    this.$message({
                        type: "info",
                        message: "已取消删除"
                    });
                });
                if ("confirm" === res) {
                    const {data: res} = await this.$http.delete(
                        "car/delete/" + id
                    );
                    if (res.code === 200) {
                        this.$message.success("车辆删除成功");
                        this.getCarList();
                    } else {
                        this.$message.error(res.msg);
                    }
                }
            },
            /*更新车辆状态*/
            async changStatus(row){
                const { data: res } = await this.$http.put(
                    "car/updateStatus/" + row.id + "/" + row.status
                );
                if (res.code !== 200) {
                    this.$message.error("更新状态失败:" + res.msg);
                    row.status = !row.status;
                } else {
                    this.$message.success("更新状态成功");
                }
            },
            update: async function () {
                this.$refs.editRuleFormRef.validate(async valid => {
                    if (!valid) {
                        return;
                    } else {
                        this.editRuleForm.status=(this.editRuleForm.status==true?1:0),
                        (this.btnLoading = true), (this.btnDisabled = true);
                        const {data: res} = await this.$http.put(
                            "car/update/" + this.editRuleForm.id,
                            this.editRuleForm
                        );
                        if (res.code === 200) {
                            this.$notify({
                                title: "成功",
                                message: "部门信息更新",
                                type: "success"
                            });
                            this.getCarList();
                        } else {
                            this.$message.error("部门信息更新失败:" + res.msg);
                        }
                        this.editRuleForm = {};
                        this.btnDisabled = false;
                        this.btnLoading = false;
                        this.editDialogVisible = false;
                    }
                });
            },
            // 编辑@param {Object} id
            edit: async function (id) {
                const {data: res} = await this.$http.get("car/edit/" + id);
                if (res.code === 200) {
                    this.editRuleForm = res.data;
                    this.editRuleForm.status=(res.data.status==1?true:false)
                } else {
                    return this.$message.error("车辆信息编辑失败" + res.msg);
                }
                this.editDialogVisible = true;
            },
            //添加
            add: function () {
                this.$refs.addRuleFormRef.validate(async valid => {
                    if (!valid) {
                        return;
                    } else {
                        (this.btnLoading = true), (this.btnDisabled = true);
                        this.addRuleForm.status=0;
                        const {data: res} = await this.$http.post(
                            "car/add",
                            this.addRuleForm
                        );
                        if (res.code === 200) {
                            this.$message.success("车辆添加成功");
                            this.addRuleForm = {};
                        } else {
                            return this.$message.error("车辆添加失败:" + res.msg);
                        }
                        this.addDialogVisible = false;
                        (this.btnLoading = false), (this.btnDisabled = false);
                    }
                });
            },
                async getCarList(){
                const { data: res } = await this.$http.get(
                    "car/findAll",
                    {
                        params: this.queryMap
                    }
                );
                if (res.code !== 200) {
                    return this.$message.error("获取列表失败");

                }else {
                    this.total = res.data.total;
                    this.carData=res.data.rows;
                }
                console.log(this.carData);
            },
            //改变页码
            handleSizeChange(newSize) {
                this.queryMap.pageSize = newSize;
                this.getCarList();
            },
            //翻页
            handleCurrentChange(current) {
                this.queryMap.pageNum = current;
                this.getCarList();
            },
            //关闭弹出框
            closeAddDialog() {
                this.$refs.addRuleFormRef.clearValidate();
                this.addRuleForm = {};
            },
            //关闭弹出框
            closeEditDialog() {
                this.$refs.editRuleFormRef.clearValidate();
                this.editRuleForm = {};
            }
        },
        created() {
            this.getCarList();
            setTimeout(() => {
                this.loading = false;
            }, 500);
        }
    };
</script>
