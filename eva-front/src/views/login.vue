<template>
  <div class="wrap">
    <div class="introduce">
      <h2>伊娃</h2>
      <h3></h3>
    </div>
    <div class="login">
      <h1>系统登录&nbsp;/&nbsp;LOGIN IN</h1>
      <div class="info-input">
        <el-input v-model="username" placeholder="请输入用户名" prefix-icon="el-icon-user-solid" maxlength="50" v-trim/>
        <el-input v-model="password" placeholder="请输入密码" type="password" prefix-icon="eva-icon-password" maxlength="30" show-password/>
        <div class="captcha-input">
          <el-input v-model="captcha.value" placeholder="图片验证码" prefix-icon="eva-icon-shield" maxlength="4" @keypress.enter.native="login"/>
          <img v-if="!captcha.loading" :src="captcha.uri" @click="refreshCaptcha">
          <span v-else><i class="el-icon-loading"></i></span>
        </div>
      </div>
      <el-button :loading="loading" @click="login">登&nbsp;&nbsp;录</el-button>
    </div>
  </div>
</template>

<script>
import { mapMutations } from 'vuex'
import { getCaptcha, loginByPassword } from '@/api/system/common'

export default {
  name: 'Login',
  data () {
    return {
      loading: false,
      username: '',
      password: '',
      // 验证码
      captcha: {
        loading: false,
        value: '',
        uuid: '',
        uri: ''
      }
    }
  },
  methods: {
    ...mapMutations(['setUserInfo']),
    // 登录
    login () {
      if (this.loading) {
        return
      }
      if (!this.__check()) {
        return
      }
      this.loading = true
      loginByPassword({
        username: this.username.trim(),
        password: this.password,
        code: this.captcha.value.trim(),
        uuid: this.captcha.uuid
      })
        .then(() => {
          window.location.href = process.env.VUE_APP_CONTEXT_PATH
        })
        .catch(e => {
          this.refreshCaptcha()
          this.$tip.apiFailed(e)
        })
        .finally(() => {
          this.loading = false
        })
    },
    // 刷新验证码
    refreshCaptcha () {
      this.captcha.loading = true
      getCaptcha()
        .then(data => {
          this.captcha.uri = data.image
          this.captcha.uuid = data.uuid
        })
        .catch(e => {
          this.$tip.apiFailed(e)
        })
        .finally(() => {
          setTimeout(() => {
            this.captcha.loading = false
          }, 150)
        })
    },
    // 登录前验证
    __check () {
      if (this.username.trim() === '') {
        this.$tip.error('请输入用户名')
        return false
      }
      if (this.password === '') {
        this.$tip.error('请输入密码')
        return false
      }
      if (this.captcha.value.trim() === '') {
        this.$tip.error('请输入图片验证码')
        return false
      }
      return true
    }
  },
  created () {
    this.refreshCaptcha()
  }
}
</script>

<style scoped lang="scss">
@import "@/assets/style/variables.scss";
$input-gap: 30px;
.wrap {
  display: flex;
  width: 100%;
  height: 100vh;
  background-image: url("../assets/images/login.jpg");
  background-repeat: no-repeat;
  background-size: auto 180%;
  background-clip: content-box;
  background-position: center;
  // 左边介绍
  .introduce {
    padding-left: 10%;
    width: 100%;
    height: 100%;
    box-sizing: border-box;
    color: #fff;
    background: rgba(0, 0, 0, 0.4);
    display: flex;
    flex-direction: column;
    justify-content: center;
    h2 {
      font-size:34px;
      font-style: italic;
      font-weight: 900;
      margin-top: 50px;
    }
    h3 {
      font-size: 49px;
      font-weight: 300;
      margin: 25px 0;
    }
  }
  // 右边登录
  .login {
    height: 100%;
    width: 38%;
    max-width: 560px;
    min-width: 460px;
    flex-shrink: 0;
    text-align: center;
    background: #fff;
    padding: 0 80px;
    display: flex;
    flex-direction: column;
    justify-content: center;
    box-sizing: border-box;
    h1 {
      font-size: 28px;
      font-weight: 500;
    }
    .info-input {
      margin-top: $input-gap;
      margin-bottom: 60px;
      /deep/ .el-input {
        margin-top: 30px;
        .el-input__inner {
          height: 50px;
          background: #F9F9F9;
          border: 1px solid transparent;
          &:focus {
            border: 1px solid $primary-color;
          }
        }
      }
    }
    // 验证码输入
    .captcha-input {
      display: flex;
      margin-top: $input-gap;
      height: 50px;
      .el-input {
        width: 100%;
        margin-top: 0;
      }
      span, img {
        width: 45%;
        height: 100%;
        flex-shrink: 0;
        margin-left: 16px;
      }
      span {
        display: flex;
        align-items: center;
        justify-content: center;
        background: #f7f7f7;
        border-radius: 8px;
      }
    }
    .el-button {
      height: 50px;
      width: 100%;
      color: #fff;
      font-size: 16px;
      background: linear-gradient(130deg, $primary-color + 20 0%, $primary-color - 20 100%);
    }
  }
}
</style>
