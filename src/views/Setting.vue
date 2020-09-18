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
  <div class="seting-box">
    <s-header :name="'账号管理'"></s-header>
    <div class="input-item">
      <van-field v-model="name" label="昵称" />
      <van-field v-model="introduceSign" label="个性签名" />
      <van-field v-model="password" type='password' label="修改密码" />
      <van-field  type="file"
                  @change="uploadFile"
                  name="请选择"
                  id="file"
                  label="上传头像"
                  accept="image/gif, image/jpeg, image/jpg, image/png, image/svg"
                  multiple="multiple" />
    </div>
    <van-button class="save-btn" color="#1baeae" type="primary" @click="save" block>保存</van-button>
    <van-button class="save-btn" color="#1baeae" type="primary" @click="logout" block>退出登录</van-button>
  </div>
</template>

<script>
import sHeader from '@/components/SimpleHeader'
import { getUserInfo, EditUserInfo, logout } from '../service/user'
import { setLocal } from '@/common/js/utils'
import { Toast } from 'vant'
export default {
  components: {
    sHeader
  },
  data() {
    return {
      name: '',
      introduceSign: '无',
      password: ''
    }
  },
  async mounted() {
    const { data } = await getUserInfo()
    this.name = data.name
    //this.introduceSign = data.introduceSign
  },
  methods: {
    async save() {
      const params = {
        introduceSign: this.introduceSign,
        nickName: this.nickName,
        password: this.password
      }
      const { data } = await EditUserInfo(params)
      Toast.success('保存成功')
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
    async logout() {
      const { resultCode } = await logout()
      if (resultCode == 200) {
        setLocal('token', '')
        window.location.href = '/'
      }
    }
  }
}
</script>

<style lang="less" scoped>
  .seting-box {
    .input-item {
      margin-top: 44px;
    }
    .save-btn {
      width: 80%;
      margin: 20px auto ;
    }
  }
</style>
