<!--
 * 严肃声明：
 * 开源版本请务必保留此注释头信息，若删除我方将保留所有法律责任追究！
 * 本系统已申请软件著作权，受国家版权局知识产权以及国家计算机软件著作权保护！
 * 可正常分享和学习源码，不得用于违法犯罪活动，违者必究！
 * Copyright (c) 2020 陈尼克 all rights reserved.
 * 版权所有，侵权必究！
 *
-->

<template>
  <div class="login">
    <s-header :name=" '登录'" :back="'/home'"></s-header>
    <img class="logo" src="//s.weituibao.com/1582958061265/mlogo.png" alt="">
    <div class="login-body login">
      <van-form @submit="onSubmit">
        <van-field
          v-model="account"
          name="account"
          label="用户名"
          placeholder="用户名"
          :rules="[{ required: true, message: '请填写用户名' }]"
        />
        <van-field
          v-model="password"
          type="password"
          name="password"
          label="密码"
          placeholder="密码"
          :rules="[{ required: true, message: '请填写密码' }]"
        />
        <div class="verify">
          <Verify ref="loginVerifyRef" @error="error" :showButton="false" @success="success" :width="'100%'" :height="'40px'" :fontSize="'16px'" :type="2"></Verify>
        </div>
        <div style="margin: 16px;">
          <router-link   class="link-register" tag="span" to="./register" >立即注册</router-link>
          <van-button round block type="info" color="#1baeae" native-type="submit">登录</van-button>
        </div>
        <div class="tableTitle"><span class="midText">其他方式</span></div>
        <div>
          <center>
            <van-button type="info" size="small" color="#04BE02" style="margin-left:15px; margin-top: 15px;">微信登录</van-button>
            <van-button type="info" size="small" style="margin-left:150px;margin-top: 15px">支付宝登录</van-button>
          </center>
        </div>
      </van-form>
    </div>
    <!--<div v-else class="login-body register">
      <van-form @submit="onSubmit">
        <van-field
          v-model="username1"
          name="username1"
          label="用户名"
          placeholder="用户名"
          :rules="[{ required: true, message: '请填写用户名' }]"
        />
        <van-field
          v-model="password1"
          type="password"
          name="password1"
          label="密码"
          placeholder="密码"
          :rules="[{ required: true, message: '请填写密码' }]"
        />
        <div class="verify">
          <Verify ref="loginVerifyRef" @error="error" :showButton="false" @success="success" :width="'100%'" :height="'40px'" :fontSize="'16px'" :type="2"></Verify>
        </div>
        <div style="margin: 16px;">
          <div class="link-login" @click="toggle('login')">已有登录账号</div>
          <van-button round block type="info" color="#1baeae" native-type="submit">注册</van-button>
        </div>
      </van-form>
    </div>-->
  </div>
</template>

<script>
  import sHeader from '@/components/SimpleHeader'
  import { login, register, getUserInfo } from '../service/user'
  import { setLocal, getLocal } from '@/common/js/utils'
  import { Toast } from 'vant'
  import Verify from 'vue2-verify'
  export default {
    data() {
      return {
        account: '',
        password: '',
        username1: '',
        password1: '',
        type: 'login',
        verify: false,

      }

    },
    components: {
      sHeader,
      Verify
    },
    methods: {
      dealTriVer() {
        this.$refs.loginVerifyRef.$refs.instance.checkCode()
      },
      toggle() {
        window.location.href = '/register'
      },
      async onSubmit(values) {
        this.dealTriVer()
        if (!this.verify) {
          Toast.fail('验证码未填或填写错误!')
          return
        }
        let data = await login({
          "account": values.account,
          "password": values.password
        })
        setLocal('token', data.token)
        window.location.href = '/'

      },
      success(obj) {
        this.verify = true
        // 回调之后，刷新验证码
        obj.refresh()
      },
      error(obj) {
        this.verify = false
        // 回调之后，刷新验证码
        obj.refresh()
      },
      // 微信授权登录  请求后端接口返回一个微信的二维码
      wxlogin () {
        User.wxlogins().then(res => {
          console.log(res)
          window.location.href = res.result.url
        })
      },
    },
  }
</script>

<style lang="less">
  .login {
    .logo {
      width: 120px;
      height: 120px;
      display: block;
      margin: 80px auto 0px;
    }
    .login-body {
      padding: 0 20px;
      width:50% ;
      margin: 0 auto;
    }
    .login {
      .link-register {
        font-size: 14px;
        margin-bottom: 20px;
        color: #1989fa;
        display: inline-block;
      }
    }
    .register {
      .link-login {
        font-size: 14px;
        margin-bottom: 20px;
        color: #1989fa;
        display: inline-block;
      }
    }
    .verify-bar-area {
      margin-top: 24px;
      .verify-left-bar {
        border-color: #1baeae;
      }
      .verify-move-block {
        background-color: #1baeae;
        color: #fff;
      }
    }
    .verify {
      >div {
        width: 100%;
      }
      display: flex;
      justify-content: center;
      .cerify-code-panel {
        margin-top: 16px;
      }
      .verify-code {
        width: 40%!important;
        float: left!important;
      }
      .verify-code-area {
        float: left!important;
        width: 54%!important;
        margin-left: 14px!important;
        .varify-input-code {
          width: 90px;
          height: 38px!important;
          border: 1px solid #e9e9e9;
          padding-left: 10px;
          font-size: 16px;
        }
        .verify-change-area {
          line-height: 44px;
        }
      }
    }
    //虚线
    .tableTitle {
      position: relative;
      margin: 0 auto;
      width: 600px;
      height: 1px;
      background-color: #d4d4d4;
      text-align: center;
      font-size: 16px;
      color: rgba(101, 101, 101, 1);
    }
    .midText {
      position: absolute;
      left: 50%;
      background-color:  #ffffff;
      padding: 0 15px;
      transform: translateX(-50%) translateY(-50%);
    }
  }
</style>
