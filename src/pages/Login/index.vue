<template>
  <div class="login-page">
    <div class="form">
      <el-form
        :model="loginForm"
        status-icon
        :rules="rules"
        ref="loginForm"
        label-width="100px"
        class="demo-loginForm"
      >
        <el-form-item label="用户名" prop="username">
          <el-input
            type="text"
            v-model="loginForm.username"
            autocomplete="off"
          ></el-input>
        </el-form-item>
        <el-form-item label="密码" prop="password">
          <el-input
            type="password"
            v-model="loginForm.password"
            autocomplete="off"
          ></el-input>
        </el-form-item>
        <el-form-item>
          <el-button type="primary" @click="submitForm('loginForm')"
            >提交</el-button
          >
        </el-form-item>
      </el-form>
    </div>
    <video
      class="bg_video"
      muted
      src="../../assets/imgs/boat.mp4"
      autoplay
      loop
      preload="auto"
    ></video>
  </div>
</template>
<script>
import { login } from "@/api"
export default {
  data() {
      // 校验用户名
      var validateUsername = (rule, value, callback) => {
        // 用户名正则，4到16位（字母，数字，下划线，减号）
        // var uPattern = /^[a-zA-Z0-9_-]{4,16}$/
        if (!value) {
          console.log(123)
          callback(new Error('请输入用户名4到16位'))
        } else {
          callback()
        }
      }
      // 校验用户密码
      var validatePassword = (rule, value, callback) => {
        if (value === '') {
          callback(new Error('请输入密码'))
        } else {
          callback()
        }
      }
      return {
        loginForm: {
          username: '',
          password: ''
        },
        rules: {
          username: [
            { validator: validateUsername, trigger: 'blur' }
          ],
          password: [
            { validator: validatePassword, trigger: 'blur' }
          ]
        }
      }
    },
  methods:  {
      submitForm(formName) {
        this.$refs[formName].validate((valid) => {
          if (valid) { //代表本地校验通过
            //打开登入加载动画

            const loading = this.$loading({
              lock: true,
              text: '正在登入...',
              spinner: 'el-icon-loading',
              background: 'rgba(0, 0, 0, 0.7)'
            })

            let { username, password } = this.loginForm;
            //发送登入请求
            login(username, password)
              .then(res => {

                //服务器响应,关闭loading动画

                loading.close()

                if (res.data.state) {
                  this.$message.success('登入成功')
                  //用户名密码正确
                  localStorage.setItem('wf-token', res.data.token)
                  //跳转到主页
                  this.$router.push("/")
                } else {
                  //用户名或者密码错误
                  this.$message.error('用户名密码错误')
                }
              })
              .catch(err => {
                console.log(err);
              })
          } else {
            console.log('error submit!!')
            return false
          }
        })
      },
      resetForm(formName) {
        this.$refs[formName].resetFields()
      }
    }
};
</script>
<style scoped>
.login-page {
  height: 100%;
  width: 100%;
  overflow: hidden;
  background-color: rgb(184, 223, 238);
}

/* 表单样式 */
.el-form {
  width: 400px;
}
.bg_video {
  display: block;
  margin: auto;
  width: 100%;
}
.form {
  position: absolute;
  left: 57%;
  top: 18%;
  width: 400px;
  height: 250px;
  background: rgba(255, 253, 253, 0.3);
  border-radius: 10px;
  padding-top: 5%;
  z-index: 1;
}
</style>
