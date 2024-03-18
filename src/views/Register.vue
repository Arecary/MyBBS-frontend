<template>
  <div class="container">
    <div style="width: 500px; height:420px; padding: 50px 30px; background-color: rgba(255,255,255,0.75); border-radius: 5px;">
      <div style="text-align: center; font-size: 50px; margin-bottom: 30px; color: #333">Register</div>
      <el-form :model="form" :rules="rules" ref="formRef">
        <el-form-item prop="username">
          <el-input size="large" style="font-size: 20px;" prefix-icon="el-icon-user" placeholder="Please enter username" v-model="form.username"></el-input>
        </el-form-item>
        <el-form-item prop="password">
          <el-input size="large" style="font-size: 20px;" prefix-icon="el-icon-lock" placeholder="Please enter password" show-password  v-model="form.password"></el-input>
        </el-form-item>
        <el-form-item prop="confirmPass">
          <el-input size="large" style="font-size: 20px;" prefix-icon="el-icon-lock" placeholder="Please confirm your password" show-password  v-model="form.confirmPass"></el-input>
        </el-form-item>
        <el-form-item>
          <el-button size="large" style="width: 100%; background-color: #333; border-color: #333; color: white" @click="register">Register</el-button>
        </el-form-item>
        <div style="display: flex; align-items: center">
          <div style="flex: 1"></div>
          <div style="flex: 1; text-align: right">
            Have a account? Please <a href="/login">login</a>
          </div>
        </div>
      </el-form>
    </div>
  </div>
</template>

<script>
export default {
  name: "Register",
  data() {
    // 验证码校验
    const validatePassword = (rule, confirmPass, callback) => {
      if (confirmPass === '') {
        callback(new Error('Please confirm password'))
      } else if (confirmPass !== this.form.password) {
        callback(new Error('The passwords entered twice are inconsistent'))
      } else {
        callback()
      }
    }
    return {
      form: {role: 'USER'},
      rules: {
        username: [
          { required: true, message: 'Please enter username', trigger: 'blur' },
        ],
        password: [
          { required: true, message: 'Please enter password', trigger: 'blur' },
        ],
        confirmPass: [
          { validator: validatePassword, trigger: 'blur' }
        ]
      }
    }
  },
  created() {

  },
  methods: {
    register() {
      this.$refs['formRef'].validate((valid) => {
        if (valid) {
          // 验证通过
          this.$request.post('/register', this.form).then(res => {
            if (res.code === '200') {
              this.$router.push('/')  // 跳转登录页面
              this.$message.success('Register successfully')
            } else {
              this.$message.error(res.msg)
            }
          })
        }
      })
    }
  }
}
</script>

<style scoped>
.container {
  height: 100vh;
  overflow: hidden;
  background-image: url("@/assets/imgs/bg1.jpg");
  background-size: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
  color: #666;
}
a {
  color: #2a60c9;
}
</style>