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
  <div class="order-box">
    <s-header :name="'我的订单'" :back="'/user'"></s-header>
    <van-tabs @change="onChangeTab"  type="card" :color="'black'" :title-active-color="'#1baeae'" class="order-tab" v-model="status">
      <van-tab title="全部" name=''></van-tab>
      <van-tab title="待付款" name="1"></van-tab>
      <van-tab title="待发货" name="2"></van-tab>
      <van-tab title="待收货" name="3"></van-tab>
      <van-tab title="交易完成" name="4"></van-tab>
      <van-tab title="退款/售后" name="5"></van-tab>
    </van-tabs>

    <div id="example-1" >
      <div  v-for="(item,index) in list" :key="index" class="order-item-box" @click="goTo(item.orderNo)">
        <div class="order-item-header">
          <span style="font-size: 15px">订单时间：{{item.createTime}}</span>
          <span style="font-size: 20px;float: right" >状态：{{item.status}}</span>
          <span style="font-size: 20px">金额（¥）：{{item.totalPrice}} 元</span>
        </div>
        <van-card
          v-for="one in item.items"
          :key="one.id"
          :currency='$'
          :num="one.goodsCount"
          :price="one.sellingPrice"
           desc="全场包邮"
          :title="one.goodsName"
          :thumb="one.goodsImg"
        />
      </div>
    </div>
  </div>
</template>

<script>
import sHeader from '@/components/SimpleHeader'
import { getOrderList } from '../service/order'

export default {
  data() {
    return {
      status: '',
      list: []
    }
  },
  components: {
    sHeader
  },
  async mounted() {
    this.loadData()
  },
  methods: {
    async loadData() {
      const { data} = await getOrderList(this.status)
      this.list=data
      console.log(this.list)
    },
    onChangeTab(name, title) {
      this.status = name
      this.loadData()
    },
    goTo(id) {
      this.$router.push({ path: `order-detail?id=${id}` })
    },
    goBack() {
      this.$router.go(-1)
    },


  }
}
</script>

<style lang="less" scoped>
  @import '../common/style/mixin';
  .order-box {
    .order-header {
      position: fixed;
      top: 0;
      left: 0;
      z-index: 10000;
      .fj();
      .wh(100%, 44px);
      line-height: 44px;
      padding: 0 10px;
      .boxSizing();
      color: #252525;
      background: #fff;
      border-bottom: 1px solid #dcdcdc;
      .order-name {
        font-size: 14px;
      }
    }
    .order-tab {
      margin-top: 44px;
      position: fixed;
      left: 0;
      z-index: 1000;
      width: 100%;
    }

    .order-item-box {
      margin: 100px 10px;
      background-color: #fff;
      .order-item-header {
        padding: 10px 20px 0 20px;
        display: flex;
        justify-content: space-between;
      }
      .van-card {
        background-color: #fff;
        margin-top: 0;
      }
    }
  }
</style>
