<template>
  <div class="login-wrap">
    <div class="form-wrap">

      <!-- 表单图片 -->
      <div class="form-head">
        <img src="./logo_index.png" alt="头条">
      </div>

      <!-- 表单 -->
      <el-form class="form-content" ref="form" :model="form">
        <!-- 手机号 -->
        <el-form-item>
          <el-input v-model="form.name" placeholder="手机号码"></el-input>
        </el-form-item>

        <!-- 验证码 -->
        <el-form-item>
          <el-col :span="16">
            <el-input v-model="form.name"></el-input>
          </el-col>
          <!-- 获取验证码 -->
          <el-col :offset="1" :span="7">
            <el-button>获取验证码</el-button>
          </el-col>
        </el-form-item>

        <!-- 用户协议 -->
        <el-form-item>
          <el-form-item>
            <el-checkbox> </el-checkbox>
            <span> 我已阅读并同意<a href="#">用户协议</a></span>
          </el-form-item>
        </el-form-item>

        <!-- 登录 -->
        <el-form-item>
          <el-button class="btn-login" type="primary" @click="onSubmit">登录</el-button>
        </el-form-item>
      </el-form>

    </div>
  </div>
</template>

<script>
export default {
  name: 'AppLogin',
  data () {
    return {
      ruleForm: {
        type: ''
      },
      form: {
        name: '',
        region: '',
        date1: '',
        date2: '',
        delivery: false,
        type: [],
        resource: '',
        desc: ''
      }
    }
  },
  methods: {
    onSubmit () {
      console.log('submit!')
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
