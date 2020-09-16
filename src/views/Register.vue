<template>
  <div class="login">
    <s-header :name="'注册'" :back="'/home'"></s-header>
    <img class="logo" src="//s.weituibao.com/1582958061265/mlogo.png" alt="">
    <div class="login-body login">
      <van-form @submit="onSubmit">
        <van-field
          v-model="account"
          name="account"
          label="用户名"
          size="small"
          placeholder="用户名"
          :rules="[{ required: true, message: '请填写用户名' }]"
        />
        <van-field
          v-model="name"
          name="name"
          label="昵称"
          placeholder="昵称"
          :rules="[{ required: true, message: '请填写昵称' }]"
        />
        <van-field
          v-model="password"
          type="password"
          name="password"
          label="密码"
          placeholder="密码"
          :rules="[{ required: true, message: '请填写密码' }]"
        />
        <van-field
          v-model="fixPassword"
          type="password"
          name="fixPassword"
          label="确认密码"
          placeholder="确认密码"
          :rules="[{ required: true, message: '请再次输入' }]"
        />
        <!--capture="camera" 相机功能   手机才可以使用-->
        <van-field
          type="file"
          @change="uploadFile"
          name="请选择"
          id="file"
          label="上传头像"
          accept="image/gif, image/jpeg, image/jpg, image/png, image/svg"
          multiple="multiple"
        />
        <!--应用的标识图片暂时注释掉-->
        <!--<img :src="headUrl" width="100px" height="100px">-->
        <van-field
          v-model="phone"
          type="number"
          name="phone"
          label="手机号码"
          placeholder="手机号码"
          @change="isTel"
          :rules="[{ required: true, message: '请输入手机号码' }]"
        />
        <van-field
          v-model="sms"
          center
          clearable
          label="短信验证码"
          placeholder="请输入短信验证码"
        >
          <van-button slot="button" size="small" type="primary">发送验证码</van-button>
        </van-field>
        <div style="margin: 16px;">
          <router-link   class="link-register" tag="span" to="./login" >已有账号？立即登录</router-link>
          <van-button round block type="info" color="#1baeae" native-type="submit">立即注册</van-button>
        </div>
      </van-form>
    </div>
  </div>
</template>
<script>
  import sHeader from '@/components/SimpleHeader'
  import {  register, getUserInfo } from '../service/user'
  import { setLocal, getLocal } from '@/common/js/utils'
  import { Toast } from 'vant'
  import Verify from 'vue2-verify'
  export default {
    data() {
      return {
        account: '',
        name: '',
        password: '',
        fixPassword: '',
        phone: '',
        sms: '',
        headUrl:'//s.weituibao.com/1582958061265/mlogo.png',
        status:1
      }
    },
    components: {
      sHeader
    },
    methods: {
      isTel(){
        if (!/^1[34578]\d{9}$/.test(this.phone)) {
          Toast.fail('请填写正确的手机号!')
          return false;
        }
        return true;
      },
      uploadFile(file){
        let $this=this;
        let oFile=document.getElementById("file").files[0]
        let form = new FormData();
        form.append('file',oFile);
        let xhr = new XMLHttpRequest();
        xhr.open('post','http://localhost:8000/file/uploadFile?type=head',true);
        xhr.timeout = 30 * 1000;
        xhr.upload.onprogress = this.progress;
        xhr.onload = function (data) {
          let url=JSON.parse(data.currentTarget.response).decryptUrl;
          $this.headUrl=url;

        };
        xhr.onerror = function (data) {
          let message=JSON.parse(data.currentTarget.response).message;
          alert(message)
        };
        xhr.upload.onloadstart = () => {
          let date = new Date().getTime();
          let initSize = 0;
        }
        xhr.send(form);
      },
      async onSubmit(values) {
        console.log(this)
        if (!this.isTel()) {
          Toast.fail('验证码未填或填写错误!')
          return
        }
        if (this.password!=this.fixPassword){
          Toast.fail('两次密码输入不一致!')
          return
        }
        const { data, resultCode } = await register({
          "account": values.account,
          "name": values.name,
          "password": values.password,
          "phone": values.phone,
          "headUrl": values.headUrl
        })
        if (resultCode==200){
          window.location.href = '/'
        }else {
          Toast.fail(data.message)
        }
      }
    },
  }
</script>
