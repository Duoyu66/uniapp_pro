<template>
  <view>
    <!-- //轮播图的区域 -->
    <swiper :indicator-dots="true" :autoplay="true" :interval="3000" :duration="1000" :circular="true">
      <swiper-item v-for="(item,index) in swiperList" :key="index">
        <navigator class="swiper-item" :url="'/subpkg/goods_detail/goods_detail?goods_id'+item.goods_id">
          <image :src="item.image_src"></image>
        </navigator>
      </swiper-item>
    </swiper>
    <!-- 分类导航区域 -->
    <view class="nav-list">
      <view class="nav-item" v-for="(item,index) in navList" :key="index" @click="navClickHandler(item)">
        <image :src="item.image_src" class="nav-img"></image>
      </view>
    </view>
    <!-- 楼层的数据 -->
    <view class="floor-list">
      <view class="floor-item" v-for="(item,index) in floorList" :key="index">
        <image :src="item.floor_title.image_src" class="floor-title"></image>
        <!-- 楼层图片区域 -->
        <view class="floor-img-box">
          <!-- 左侧大盒子 -->
          <navigator class="letf-img-box" :url="item.product_list[0].url">
            <image :src="item.product_list[0].image_src" :style="{width: item.product_list[0].image_width + 'rpx'}"
              mode="widthFix"></image>
          </navigator>
          <!-- 右侧盒子 -->
          <view class="right-img-box">
            <navigator class="right-img-item" v-for="(c2,index2) in item.product_list" :key="index2" v-if="index2!=0"
              :url="c2.url">
              <image :src="c2.image_src" mode="widthFix" :style="{width: c2.image_width + 'rpx'}"></image>
            </navigator>
          </view>

        </view>
      </view>
    </view>
  </view>
</template>

<script>
  export default {
    data() {
      return {
        //这是轮播图的数据列表
        swiperList: [],
        //分类导航的列表
        navList: [],
        //楼层的数据
        floorList: []
      };
    },
    onLoad() {
      //调用方法，获取轮播图的数据
      this.getSwiperList();
      this.getNavList();
      this.getFloorList();
    },
    methods: {
      async getSwiperList() {
        const {
          data: res
        } = await uni.$http.get('/api/public/v1/home/swiperdata')
        if (res.meta.status !== 200) {
          return uni.$showMsg()
        }
        this.swiperList = res.message
      },
      async getNavList() {
        const {
          data: res
        } = await uni.$http.get('/api/public/v1/home/catitems')

        if (res.meta.status !== 200) return uni.$showMsg()
        this.navList = res.message
      },
      //nav-item 项被点击的事件处理函数
      navClickHandler(item) {
        //判断是哪个 nsv
        if (item.name === '分类') {
          uni.switchTab({
            url: "/pages/cate/cate"
          })
        }
      },
      //获取首页楼层方法的调用
      async getFloorList() {
        const {
          data: res
        } = await uni.$http.get('/api/public/v1/home/floordata')
        if (res.meta.status !== 200) return uni.$showMsg()

        // 通过双层 forEach 循环，处理 URL 地址
        res.message.forEach(floor => {
          floor.product_list.forEach(prod => {
            prod.url = '/subpkg/goods/goods?' + prod.navigator_url.split('?')[1]
          })
        })
        this.floorList = res.message

      }
    }
  }
</script>

<style lang="scss">
  //轮播图
  swiper {
    height: 330rpx;

    .swiper-item,
    image {
      width: 100%;
      height: 100%;
    }
  }

  //分类
  .nav-list {
    display: flex;
    justify-content: space-around;
    margin: 15px 0;

    .nav-img {
      width: 128rpx;
      height: 140rpx;
    }
  }

  // 楼层
  .floor-title {
    width: 100%;
    height: 60px;
    // display: flex;
  }

  .right-img-box {
    display: flex;
    flex-wrap: wrap;
    // justify-content: space-around;
  }

  .floor-img-box {
    display: flex;
    // padding-left: 10rpx;
  }
</style>
