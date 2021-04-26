<template>
  <div class="login-wrap">
    <div class="form-wrap">

      <!-- 表单图片 -->
      <div class="form-head">
        <img src="./logo_index.png" alt="头条">
      </div>

      <!-- 表单 -->
      <el-form
       class="form-content"
       :model="form"
       :rules="rules"
       ref="form"
      >
        <!-- 手机号 -->
        <el-form-item prop="mobile">
          <el-input v-model="form.mobile" placeholder="手机号码"></el-input>
        </el-form-item>

        <!-- 验证码 -->
        <el-form-item prop="code">
          <el-col :span="16">
            <el-input v-model="form.code"  placeholder="验证码"></el-input>
          </el-col>
          <!-- 获取验证码 -->
          <el-col :offset="1" :span="7">
            <!-- <el-button @click="handleSendCode">获取验证码</el-button> -->
            <el-button
              @click="handleSendCode"
              :disabled="!!codeTimer"
            >{{ codeTimer ? `剩余${codeTimeSeconds}秒` : '获取验证码' }}</el-button>
          </el-col>
        </el-form-item>

        <!-- 用户协议 -->
        <el-form-item prop="agree">
          <el-checkbox v-model="form.agree" class="agree-checkbox"> </el-checkbox>
          <span class="agree-text"> 我已阅读并同意<a href="#">用户协议</a></span>
        </el-form-item>

        <!-- 登录 -->
        <el-form-item>
          <el-button class="btn-login" type="primary" @click="handleLogin">登录</el-button>
        </el-form-item>
      </el-form>

    </div>
  </div>
</template>

<script>
import axios from 'axios'
import '@/vendor/gt'
const initCodeTimeSeconds = 10 // 倒计时初始时长

export default {
  name: 'AppLogin',
  data () {
    return {
      form: { // 表单数据的项
        mobile: '',
        code: '',
        agree: ''
      },
      rules: { // 验证规则
        mobile: [
          { required: true, message: '请输入手机号码', trigger: 'blur' },
          { pattern: /\d{11}/, message: '必须为11位', trigger: 'blur' }
        ],
        code: [
          { required: true, message: '请输入验证码', trigger: 'blur' },
          { len: 6, message: '必须为6位', trigger: 'blur' }
        ],
        agree: [
          { required: true, message: '请同意用户协议' },
          { pattern: /true/, message: '请同意用户协议' }
        ]
      },
      codeTimer: null, // 倒计时定时器
      codeTimeSeconds: initCodeTimeSeconds // 倒计时时间
    }
  },
  methods: {
    handleLogin () {
      this.$refs.form.validate(valid => {
        if (!valid) {
          return
        }
        this.submitLogin() // 表单验证通过提交登录请求
      })
    },

    /**
     * 表单验证通过提交登录请求
     */
    submitLogin () {
      axios({
        method: 'POST',
        url: 'http://ttapi.research.itcast.cn/mp/v1_0/authorizations',
        data: this.form
      }).then(res => {
        console.log(res.data)
        this.$message({
          message: '恭喜你，这是一条成功消息',
          type: 'success'
        })
        this.$router.push({
          name: 'home'
        })
      }).catch(e => {
        this.$message.error('登陆失败,手机号或验证码错误')
      })
    },

    /**
     * 点击发送验证码事件
     */
    handleSendCode () {
      // 发送验证码时, 验证手机号是否有效
      // eslint-disable-next-line dot-notation
      this.$refs['form'].validateField('mobile', errorMessage => {
        if (errorMessage.trim().length > 0) {
          return
        }
        this.showGeetest() // 初始化验证码 & 显示人机交互 & 发送短信
      })
    },

    /**
     * 初始化验证码 & 显示人机交互 & 发送短信
     */
    showGeetest () {
      const { mobile } = this.form
      axios({
        method: 'GET',
        url: `http://ttapi.research.itcast.cn/mp/v1_0/captchas/${mobile}`
      }).then(res => {
        console.log(res.data)
        const { data } = res.data
        window.initGeetest({
          // 以下配置参数来自服务端 SDK
          gt: data.gt,
          challenge: data.challenge,
          offline: !data.success,
          new_captcha: data.new_captcha,
          product: 'bind'
        }, captchaObj => {
          // 这里可以调用验证实例 captchaObj 的实例方法
          captchaObj.onReady(() => {
            // 验证码ready之后才能调用verify方法显示验证码
            captchaObj.verify()
          }).onSuccess(() => {
            // 人机交互成功  收到数据并发送发短信的接口
            console.log(captchaObj.getValidate())
            const {
              geetest_challenge: challenge,
              geetest_seccode: seccode,
              geetest_validate: validate
            } = captchaObj.getValidate()
            axios({
              method: 'GET',
              url: `http://ttapi.research.itcast.cn/mp/v1_0/sms/codes/${mobile}`,
              params: {
                challenge,
                validate,
                seccode
              }
            }).then(res => {
              // 发送短信成功, 开始倒计时
              this.codeCountDown() // 倒计时
            })
          }).onError(function () {
          // your code
          })
        })
      })
    },

    /**
     * 倒计时
     */
    codeCountDown () {
      this.codeTimer = window.setInterval(() => {
        this.codeTimeSeconds--
        if (this.codeTimeSeconds <= 0) {
          window.clearInterval(this.codeTimer)
          this.codeTimer = null
          this.codeTimeSeconds = 10
        }
      }, 1000)
    }
  }
}
</script>

<style lang="less" scoped>
.login-wrap {
  height: 100%;
  background-image: url("./login_bg.jpg");
  display: flex;
  justify-content: center;
  align-items: center;
  .form-wrap {
    width: 400px;
    background-color: #fff;
    padding: 20px;
    border-radius: 10px;
    .btn-login {
      width: 100%;
    }
  }
  .form-head {
    display: flex;
    justify-content: center;
    align-items: center;
    margin-bottom: 10px;
    img {
      width: 200px;
    }
  }
}
</style>
