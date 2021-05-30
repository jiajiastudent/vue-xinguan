<template>
  <div id="login">
    <h1 style="position: absolute;color: #fff;left: 50%;transform: translateX(-50%); top: -110px;">疫情物资收发调度系统</h1>
    <el-form :model="userLoginForm" :rules="loginRules" status-icon
      ref="userLoginFormRef" label-position="left" label-width="0px" class="demo-ruleForm login-page">
      <h3 class="title">登录</h3>
      <el-form-item prop="username">
        <el-input type="text" @keyup.enter.native="handleSubmit" v-model="userLoginForm.username"
          auto-complete="off" placeholder="用户名" prefix-icon="iconfont el-icon-user"></el-input>
      </el-form-item>
      <el-form-item prop="password">
        <el-input @keyup.enter.native="handleSubmit" type="password" v-model="userLoginForm.password"
          auto-complete="off" placeholder="密码" prefix-icon="el-icon-suitcase-1"></el-input>
      </el-form-item>
      <div></div>

<!--      <el-checkbox v-model="checked" class="rememberme">记住密码</el-checkbox>-->
      <el-form-item style="width:100%;">
        <div style="float:right;">
          <el-button type="primary" @click="handleSubmit" :loading="loading">登录</el-button>
          <el-button type="info"   @click="addDialogVisible=true">注册</el-button>
        </div>
      </el-form-item>
    </el-form>
    <!-- 添加对话框 -->
    <el-dialog title="添加用户" @close="closeDialog" :visible.sync="addDialogVisible" width="50%;">
      <!-- 表单 -->
      <span>
          <el-form :model="addForm" :label-position="labelPosition" :rules="addFormRules"   ref="addFormRef" label-width="80px">
            <el-row>
              <el-col :span="10">
                <div class="grid-content bg-purple">
                  <el-form-item label="用户名" prop="username">
                    <el-input v-model="addForm.username"></el-input>
                  </el-form-item>
                </div>
              </el-col>
              <el-col :span="12">
                         <el-form-item prop="birth" label="生日">
                            <el-date-picker type="date" value-format="yyyy年MM月dd日" placeholder="选择出生日期" v-model="addForm.birth" style="width: 100%;"></el-date-picker>
                        </el-form-item>
<!--                <div class="grid-content bg-purple-light">-->
<!--                  <el-form-item label="部门" prop="departmentId">-->
<!--                    <el-select v-model="addForm.departmentId" placeholder="请选择所属部门">-->
<!--                      <el-option v-for="department in departments" :key="department.id" :label="department.name" :value="department.id"></el-option>-->
<!--                    </el-select>-->
<!--                  </el-form-item>-->
<!--                </div>-->
              </el-col>
            </el-row>
            <el-row>
              <el-col :span="10">
                <div class="grid-content bg-purple">
                  <el-form-item label="昵称" prop="nickname">
                    <el-input v-model="addForm.nickname"></el-input>
                  </el-form-item>
                </div>
              </el-col>
              <el-col :span="12">
                <div class="grid-content bg-purple-light">
                  <el-form-item label="性别" prop="sex">
                    <el-radio-group v-model="addForm.sex">
                      <el-radio :label="1">男</el-radio>
                      <el-radio :label="0">女</el-radio>
                    </el-radio-group>
                  </el-form-item>
                </div>
              </el-col>
            </el-row>
            <el-form-item label="密码" prop="password">
              <el-input v-model="addForm.password"></el-input>
            </el-form-item>
            <el-form-item label="邮箱" prop="email">
              <el-input v-model="addForm.email"></el-input>
            </el-form-item>
            <el-form-item label="手机" prop="phoneNumber">
              <el-input v-model="addForm.phoneNumber"></el-input>
            </el-form-item>

          </el-form>
        </span>
      <span slot="footer" class="dialog-footer">
          <el-button @click="addDialogVisible = false">取 消</el-button>
          <el-button type="primary" @click="addUser" :loading="btnLoading" :disabled="btnDisabled">确 定</el-button>
        </span>
    </el-dialog>
    <!-- 验证码 -->
    <Vcode :show="isShow" @success="success" @close="close" :canvasWidth="500" :canvasHeight="350"/>
    <el-dialog title="提示" :visible.sync="dialogVisible" width="45%">
      <span>后端采用SpringBoot，Shiro，通用Mapper开发API接口，前端Vue
      </span>
      <el-table border :data="tableData" style="width: 100%">
        <el-table-column prop="date" label="账号" width="180"></el-table-column>
        <el-table-column prop="name" label="密码" width="180"></el-table-column>
        <el-table-column prop="address" label="描述"></el-table-column>
      </el-table>
      <span slot="footer" class="dialog-footer">
        <el-button @click="dialogVisible = false">点个赞</el-button>
        <el-button type="primary" @click="dialogVisible = false">去登入</el-button>
      </span>
    </el-dialog>
  </div>
</template>
<script>
import axios from "axios";
import Vcode from "vue-puzzle-vcode";
export default {

  data() {
    var checkEmail = (rule, value, callback) => {
      const mailReg = /^([a-zA-Z0-9_-])+@([a-zA-Z0-9_-])+(.[a-zA-Z0-9_-])+/;
      if (!value) {
        return callback(new Error("邮箱不能为空"));
      }
      setTimeout(() => {
        if (mailReg.test(value)) {
          callback();
        } else {
          callback(new Error("请输入正确的邮箱格式"));
        }
      }, 100);
    };
    var checkPhone = (rule, value, callback) => {
      const phoneReg = /^1[34578]\d{9}$$/;
      if (!value) {
        return callback(new Error("电话号码不能为空"));
      }
      setTimeout(() => {
        // Number.isInteger是es6验证数字是否为整数的方法,实际输入的数字总是识别成字符串
        // 所以在前面加了一个+实现隐式转换
        if (!Number.isInteger(+value)) {
          callback(new Error("请输入数字值"));
        } else {
          if (phoneReg.test(value)) {
            callback();
          } else {
            callback(new Error("电话号码格式不正确"));
          }
        }
      }, 100);
    };
    return {
      btnDisabled:false,
      closeDialog:false,
      btnLoading:false,
      departments: [],
      labelPosition: "right", //lable对齐方式
      addFormRules: {
        username: [
          { required: true, message: "请输入用户名", trigger: "blur" },
          { min: 2, max: 10, message: "长度在 2 到 10 个字符", trigger: "blur" }
        ],
        password: [
          { required: true, message: "请输入密码", trigger: "blur" },
          { min: 3, max: 12, message: "长度在 3 到 12 个字符", trigger: "blur" }
        ],
        sex: [{ required: true, message: "请选择性别", trigger: "blur" }],
        birth: [{ required: true, message: "请填写出生日期", trigger: "blur" }],
        email: [{ required: true, validator: checkEmail, trigger: "blur" }],
        phoneNumber: [{required: true, message: "请输入正确格式联系方式", validator: checkPhone, trigger: "blur"}],
        nickname: [
          { required: true, message: "请输入昵称", trigger: "blur" },
          { min: 5, max: 10, message: "长度在 5 到 10 个字符", trigger: "blur" }
        ]
      }, //添加表单验证规则
      addForm: {username: "", nickname: "", password: "", email: "", phoneNumber: "", sex: "", birth: "",departmentId:2}, //添加表单
      addDialogVisible: false, //添加对话框,
      isShow: false,
      dialogVisible: false,
      imgCode: undefined,
      //表单用户登入数据
      loading: false,
      userLoginForm: {username: "admin", password: "123456"},
      checked: true,
      //验证规则
      loginRules: {
        username: [
          { required: true, message: "请输入用户名", trigger: "blur" },
          { min: 3, max: 12, message: "长度在 3 到 12 个字符", trigger: "blur" }
        ],
        password: [
          { required: true, message: "请输入用户密码", trigger: "blur" },
          { min: 6, max: 15, message: "长度在 6 到 15 个字符", trigger: "blur" }
        ]
      },
      tableData: [
        {date: "employee1", name: "123456", address: "系统入库操作员"},
        {date: "employee2", name: "123456", address: "系统信息维护员"},
        {date: "employee3", name: "123456", address: "系统监控员"},
        {date: "employee4", name: "123456", address: "物资审核员"}
      ]
    };
  },
  components: {
    Vcode
  },
  methods: {
    /* 添加用户 */
    async addUser() {
      this.$refs.addFormRef.validate(async valid => {
        if (!valid) {
          return;
        } else {
          this.btnLoading = true;
          this.btnDisabled = true;
          const { data: res } = await this.$http.post("user/add", this.addForm);
          if (res.code == 200) {
            this.$notify.success({
              title:'操作成功',
              message:'用户添加成功',
            });
            this.addForm = {};
          } else {
            return this.$message.error("用户添加失败:" + res.msg);
          }
          this.addDialogVisible = false;
          this.btnLoading = false;
          this.btnDisabled = false;
        }
      });
    },
    //登入提交
    handleSubmit: function() {
      this.$refs.userLoginFormRef.validate(valid => {
        if (!valid) {
          return;
        } else {
          this.isShow = true;
        }
      });
    },
    //重置表单
    resetForm: function() {
      this.$refs.userLoginFormRef.resetFields();
    },

    //验证成功
    async success() {
      this.loading = true;
      //发起登入请求
      const { data: res } = await this.$http.post(
        "user/login?username=" + this.userLoginForm.username +
          "&password=" + this.userLoginForm.password
      );
      if (res.code == 200) {
        this.$message({
          title: "登入成功",
          message: "欢迎你进入系统",
          type: "success"
        });
        //保存token
        localStorage.setItem("JWT_TOKEN", res.data);
        this.getUserInfo();
      } else {
        this.isShow = false;
        this.$message.error({
          title: "登入失败",
          message: res.msg,
          type: "error"
        });
      }
      this.loading = false;
    },



    /* 加载所有部门*/
    // async getDepartmets() {
    //   const { data: res } = await this.$http.get("department/findAll");
    //   if (res.code !== 200) return this.$message.error("获取部门列表失败");
    //   this.departments = res.data;
    // },
    /*获取用户信息*/
    async getUserInfo() {
      const { data: res } = await this.$http.get("user/info");
      if (res.code !== 200)
        return this.$message.error("获取用户信息失败:" + res.msg);
      this.userInfo = res.data;
      //保存用户
      this.$store.commit("setUserInfo", res.data);
      //跳转到home
      this.$router.push("/home");
    },
    close() {
      this.isShow = false;
    }
  },
  created() {
    // this.getDepartmets();
    setTimeout(() => {
      this.loading = false;
    }, 500);
  }
};
</script>

<style scoped>
.login-container {
  width: 100%;
  height: 100%;

}
#login{
  position: relative;
}

.login-page {
  position: relative;
  -webkit-border-radius: 5px;
  border-radius: 5px;
  margin: 190px auto;
  width: 370px;
  padding: 40px 35px 15px;
  background: #fff;
  border: 1px solid #eaeaea;
}
label.el-checkbox.rememberme {
  margin: 0px 0px 15px;
  text-align: left;
}
</style>


