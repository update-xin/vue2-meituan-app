<template>
    <div class="page-register">
            <div class="header">
                <header>
                    <a href="#" class="site-logo" @click="toHome"></a>
                    <div class="login">
                        <span>已有美团账号?</span>
                        <router-link :to="{name:'login'}">登录</router-link>
                    </div>
                </header>
            </div>
            <div class="content-form">
                <el-form
                    :model="registerForm"
                    status-icon
                    :rules="rules"
                    ref="registerForm"
                    label-width="100px"
                    class="demo-ruleForm"
                >
                <el-form-item label="用户名" prop="userName">
                    <el-input type="text" v-model="registerForm.userName" autocomplete="off"></el-input>
                </el-form-item>
                <el-form-item label="密码" prop="password">
                    <el-input type="password" v-model="registerForm.password" autocomplete="off" @input="input"></el-input>
                    <div class="pw-strength">
                        <div :class="['bar',strengthClass]"></div>
                        <div class="letter">
                            <span>弱</span>
                            <span>中</span>
                            <span>强</span>
                        </div>
                    </div>
                </el-form-item>
                <el-form-item label="确认密码" prop="rePassword">
                    <el-input type="password" v-model="registerForm.rePassword" autocomplete="off"></el-input>
                </el-form-item>
                <el-form-item>
                    <el-button @click="submitForm('registerForm')">同意以下协议并注册</el-button>
                </el-form-item>
                </el-form>
                <div class="term">
                    <a class="f1" href="https://rules-center.meituan.com/rules-detail/4" target="_blank">《美团用户服务协议》</a>
                    <a class="f1" href="https://rules-center.meituan.com/rules-detail/2" target="_blank">《美团隐私政策》</a>
                </div>
            </div>
            <footer class="footer--mini">
                <p class="copyright">
                    ©<a class="f1" href="https://www.meituan.com">meituan.com</a>&nbsp;
                    <a class="f1" target="_blank" href="http://www.miibeian.gov.cn/">京ICP证070791号</a>&nbsp;
                    <span class="f1">京公网安备11010502025545号</span>
                </p>
            </footer>
    </div>
</template>

<script>
import api from '@/api'
export default {
  data () {
    var validateUser = (rule, value, callback) => {
      if (value === '') {
        callback(new Error('请输入用户名'))
      } else if (value.length < 4 || value.length > 16) {
        callback(new Error('用户名必须为4-16位的字母数字下划线组成'))
      } else {
        callback()
      }
    }
    var validatePass = (rule, value, callback) => {
      if (value === '') {
        callback(new Error('请输入密码'))
      } else if (value.length < 4 || value.length > 16) {
        callback(new Error('密码必须为4-16位的字母数字下划线组成'))
      } else {
        if (this.registerForm.rePassword !== '') {
          this.$refs.registerForm.validateField('rePassword')
        }
        callback()
      }
    }
    var validatePass2 = (rule, value, callback) => {
      if (value === '') {
        callback(new Error('请再次输入密码'))
      } else if (value !== this.registerForm.password) {
        callback(new Error('两次输入密码不一致'))
      } else {
        callback()
      }
    }
    return {
      registerForm: {
        userName: '',
        password: '',
        rePassword: ''
      },
      strengthClass: '',
      rules: {
        userName: [{ validator: validateUser, trigger: 'blur' }],
        password: [{ validator: validatePass, trigger: 'blur' }],
        rePassword: [{ validator: validatePass2, trigger: 'blur' }]
      }
    }
  },
  methods: {
    input () {
      if (this.registerForm.password.length > 20) {
        this.strengthClass = 'strong'
      } else if (this.registerForm.password.length < 6) {
        this.strengthClass = 'week'
      } else if (this.registerForm.password.length >= 6 && this.registerForm.password.length <= 20) {
        this.strengthClass = 'normal'
      }
    },
    submitForm (formName) {
      this.$refs[formName].validate((valid) => {
        if (valid) {
          api.register({
            params: this.registerForm
          }).then(res => {
            if (res.data.status === 'success') {
              this.$router.push({ name: 'login' })
            } else {
              alert(res.data.msg)
            }
          })
        } else {
          console.log('error submit!!')
          return false
        }
      })
    },
    toHome () {
      this.$router.push({ name: 'index' })
    }
  }
}
</script>

<style lang="scss">
    @import "@/assets/css/register/index.scss";
</style>
