<template>
  <view>
    <my-address></my-address>
    <!-- 商品列表的标题区域 -->
    <view class="cart-title">
      <!-- 左侧的图标 -->
      <uni-icons type="shop" size="18"></uni-icons>
      <!-- 右侧的文本 -->
      <text class="cart-title-text">购物车</text>
    </view>
    <uni-swipe-action>
      <block v-for="(goods,index) in cart" :key="index">
        <uni-swipe-action-item :right-options="options" @click="swiperActionClickHandler(goods)">
          <my-goods :goods="goods" :show-radio="true" :show-num="true" @radio-change="radioChangeHandler"
            @num-change="numberChangeHandler"></my-goods>
        </uni-swipe-action-item>

      </block>
    </uni-swipe-action>
  </view>
</template>

<script>
  import {
    mapGetters,
    mapState,
    mapMutations
  } from 'vuex'
  import badgeMix from '@/mixins/tabbar-badge.js'
  export default {
    mixins: [badgeMix],
    data() {
      return {
        options: [{
          text: '删除',
          style: {
            backgroundColor: '#C00000'
          }
        }]
      };
    },
    computed: {
      ...mapState('m_cart', ['cart'])
    },
    methods: {
      // 点击了滑动操作按钮
      swiperActionClickHandler(goods) {
        console.log(goods)
      },
      ...mapMutations('m_cart', ['updateGoodsState', 'updateGoodsCount']),
      // 商品的勾选状态发生了变化
      radioChangeHandler(e) {
        this.updateGoodsState(e)
      },
      // 商品的数量发生了变化
      numberChangeHandler(e) {
        console.log(e)
      },


    },
  }
</script>

<style lang="scss">
  .cart-title {
    height: 40px;
    display: flex;
    align-items: center;
    border-bottom: 1px solid #EFEFEF;

    .cart-title-text {
      font-size: 14px;
      margin-left: 10px;

    }
  }
</style>
