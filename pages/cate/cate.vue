<template>
  <view>
    <!-- 自定义组件 -->
    <view class="search-box">
      <view class="my-search-container" @click="goSearch">
        <view class="my-search-box">
          <uni-icons type="search" size="30"></uni-icons>
          <text class="placeholder">搜索</text>
        </view>
      </view>
    </view>

    <!-- 中间部分 -->
    <view class="scroll-view-container">
      <!-- 左侧的滚动视图区域 -->
      <scroll-view class="left-scroll-view" scroll-y :style="{height: wh + 'px'}">
        <block v-for="(c1,index) in cateList" :key="index">
          <view :class="['left-scroll-view-item',index===active?'active':'']" @click="activeChanged(index)">
            {{c1.cat_name}}
          </view>
        </block>
      </scroll-view>
      <!-- 右侧的滚动视图区域 -->
      <scroll-view class="right-scroll-view" scroll-y :style="{height: wh + 'px'}" :scroll-top="scrollTop">
        <view class="cate-lv2-title" v-for="(item,index2) in cateList2" :key="index2">
          <!-- 二级 的标题 -->
          <view class="cate-lv2-title">
            / {{item.cat_name}} /
          </view>

          <view class="cate-lv3-list">
            <!-- 三级 分类 -->
            <view class="cate-lv3-item" v-for="(c3,index3) in item.children" :key="index3" @click="gotoGoods(c3)">
              <image :src="c3.cat_icon"></image>
              <text> {{c3.cat_name}}</text>
              <view>

              </view>
            </view>
          </view>
          <!--  -->
        </view>

      </scroll-view>
    </view>
  </view>
</template>


<script>
  export default {
    data() {
      return {
        wh: 0,
        cateList: [],
        active: 0,
        cateList2: [],
        // 滚动条距离顶部的距离
        scrollTop: 0
      };
    },
    onLoad() {
      const sysInfo = uni.getSystemInfoSync()
      this.wh = sysInfo.windowHeight
      this.getCateList();
    },
    methods: {
      async getCateList() {
        const {
          data: res
        } = await uni.$http.get('/api/public/v1/categories')
        if (res.meta.status !== 200) return uni.$showMsg()
        this.cateList = res.message
        this.cateList2 = res.message[0].children
      },
      activeChanged(value) {
        this.active = value
        this.cateList2 = this.cateList[value].children

        // 让 scrollTop 的值在 0 与 1 之间切换
        this.scrollTop = this.scrollTop === 0 ? 1 : 0
      },
      gotoGoods(c3) {
        uni.navigateTo({
          url: '/subpkg/goods/goods?cid=' + c3.cat_id
        })
      },
      goSearch() {
        uni.navigateTo({
          url: '/subpkg/search/search'
        })
      }
    }
  }
</script>

<style lang="scss">
  // 搜索框
  .search-box {
    position: sticky;
    top: 0;
    z-index: 99;
  }

  .my-search-container {
    background-color: #c00000;
    height: 50px;
    padding: 0 10px;
    display: flex;
    align-items: center;
  }

  .my-search-box {
    height: 36px;
    background-color: #ffffff;
    border-radius: 15px;
    width: 100%;
    display: flex;
    align-items: center;
    justify-content: center;

    .placeholder {
      font-size: 15px;
      margin-left: 5px;
    }
  }


  // 三级分类
  .cate-lv3-list {
    display: flex;
    flex-wrap: wrap;
    // justify-content: center;

    .cate-lv3-item {
      width: 33.33%;
      display: flex;
      flex-direction: column;
      justify-content: center;
      align-items: center;
      margin-bottom: 10px;

      image {
        width: 60px;
        height: 60px;
      }

      text {
        font-size: 12px;
      }
    }
  }

  // 二级分类
  .cate-lv2-title {
    font-size: 12px;
    font-weight: bold;
    text-align: center;
    padding: 15px 0;
  }

  .scroll-view-container {
    display: flex;

    .left-scroll-view {
      width: 120px;

      .left-scroll-view-item {
        line-height: 60px;
        background-color: #f7f7f7;
        text-align: center;
        font-size: 12px;

        // 激活项的样式
        &.active {
          background-color: #ffffff;
          position: relative;

          // 渲染激活项左侧的红色指示边线
          &::before {
            content: ' ';
            display: block;
            width: 3px;
            height: 30px;
            background-color: #c00000;
            position: absolute;
            left: 0;
            top: 50%;
            transform: translateY(-50%);
          }
        }
      }
    }
  }
</style>
