<template>
  <div class="parent">
    <div class="head">
      <Header></Header>
    </div>
    <div class="body">

      <el-form :model="ruleForm" status-icon :rules="rules" ref="ruleForm" label-width="100px" class="demo-ruleForm">
        <el-form-item label="邮箱" prop="email">
          <el-input v-model="ruleForm.email" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="密码" prop="pass">
          <el-input type="password" v-model="ruleForm.password" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item>
          <el-button type="primary" @click="submitForm('ruleForm')">提交</el-button>
          <el-button @click="resetForm('ruleForm')">重置</el-button>
        </el-form-item>

        <el-form-item>
          <span style="font-size: 13px">首次使用？</span>
          <el-button type="text" @click="register">点我注册</el-button>
        </el-form-item>
      </el-form>

    </div>


  </div>
</template>

// 改用带表单校验

<script>
import Header from "../components/Header";

export default {
  name: "Login",
  components: {Header},
  data() {

    var validatePass = (rule, value, callback) => {
      if (value === '') {
        callback(new Error('请输入密码'));
      } else {
        if (this.ruleForm.checkPass !== '') {
          this.$refs.ruleForm.validateField('checkPass');
        }
        callback();
      }
    };
    var validatePass2 = (rule, value, callback) => {
      if (value === '') {
        callback(new Error('请再次输入密码'));
      } else if (value !== this.ruleForm.password) {
        callback(new Error('两次输入密码不一致!'));
      } else {
        callback();
      }
    };
    var validataEmail = (rule, value, callback) => {
      if (value === '' ) {
        callback( new Error('请输入邮箱'));
      }
      var reg = new RegExp("^[a-zA-Z0-9_-]+@[a-zA-Z0-9_-]+(\\.[a-zA-Z0-9_-]+)+$");
      if( !reg.test(value) ) { callback( new Error("请输入正确的邮箱")); }
      else { callback(); }
    }
    return {
      ruleForm: {
        email: '',
        password: ''
      },
      rules: {
        pass: [
          {validator: validatePass, trigger: 'blur'}
        ],
        checkPass: [
          {validator: validatePass2, trigger: 'blur'}
        ],
        email: [
          {validator: validataEmail, trigger: 'blur'}
        ]
      }
    }
  },
  methods: {
    submitForm(formName) {
      this.$refs[formName].validate((valid) => {
        if (valid) {
          console.log(this.ruleForm);
          this.$axios.post("/geeker/api/login", this.ruleForm).then(res=> {
            console.log(res.data);
            if (res.data.msg != "success") {
              alert(res.data.msg);
            } else {
              this.$router.push({name: "Tasks"});
              this.$store.state.isLogin = true;
              this.$store.state.user.email = this.ruleForm.email;
              this.$store.commit("SET_TOKEN", res.data.result.userToken );
              localStorage.setItem("userToken", res.data.result.userToken);
              localStorage.setItem("email", this.ruleForm.email);
            }
          })
          console.log(this.ruleForm);
        } else {
          console.log('error submit!!');
          return false;
        }
      });
    },
    resetForm(formName) {
      this.ruleForm = {};
    },
    register() {
      this.$router.push({name: "Register"})
    }
  }
}
</script>

<style scoped>
.parent {
  display: grid;
  grid-template-columns: repeat(7, 1fr);
  grid-template-rows: repeat(9, 1fr);
  grid-column-gap: 0px;
  grid-row-gap: 0px;
}

.body {
  grid-area: 3 / 3 / 7 / 6;
}

.head {
  grid-area: 1 / 1 / 2 / 8;
}
</style>
