<template>
  <scroll-view @scrolltolower="handleToLower" class="recommend-view" scroll-y v-if="isDataReady">
    <!-- 推荐 开始 -->
    <view class="recommend-wrap">
      <navigator 
        class="recommend-items" 
        v-for="item in recommendList" 
        :key="item.id"
        :url="`/pages/album/index?id=${item.target}`"
      >
        <image mode="widthFix" :src="item.thumb"/>
      </navigator>
    </view>
    <!-- 推荐 结束 -->
    
    <!-- 月份 开始 -->
    <view class="month-wrap">
      <view class="month-title">
        <view class="month-title-info">
          <view class="month-info">
            <text> {{monthes.DD}} / </text>
            {{monthes.MM}} 月
          </view>
          <view class="month-text"> {{monthes.title}} </view>
        </view>
        <view class="month-title-more">更多 > </view>
      </view>
      <view class="month-content">
        <view class="month-content-item" v-for="(item, index) of monthes.items" :key="item.id">
          <go-detail :list="monthes.items" :index="index">
            <image mode="aspectFill" :src="item.img + item.rule.replace('$<Height>', 360)"/>
          </go-detail>
        </view>
      </view>
    </view>
    <!-- 月份 结束 -->

    <!-- 热门 开始 -->
    <view class="hots_wrap">
      <view class="hots_title">
        <text> 热门 </text>
      </view>
      <view class="hots_content">
        <view class="hot_item" v-for="(item, index) of hots" :key="item.id">
          <go-detail :list="hots" :index="index">
            <image mode="widthFix" :src="item.thumb"/>
          </go-detail>
        </view>
      </view>
    </view>
    <!-- 热门 结束 -->
  </scroll-view>
</template>

<script>
import moment from 'moment'

import goDetail from '@/components/goDetail'

export default {
  components: {
    goDetail
  },
  data () {
    return {
      // 推荐列表
      recommendList: [],
      // 月份
      monthes: {},
      // 热门
      hots: [],
      // 请求参数
      params: {
        limit: 30,
        order: 'hot',
        skip: 0
      },
      // 是否还有数据可以加载
      hasMore: true
    }
  },
  computed: {
    isDataReady () {
      return this.recommendList.length
    }
  },
  methods: {
    getHomeRecommendData () {
      this.request({
      url: 'http://157.122.54.189:9088/image/v3/homepage/vertical',
      data: this.params
    }).then( (res) => {
        // 判断有没有下一页数据
        if (res.data.res.vertical.length === 0) {
          this.hasMore = false
          uni.showToast({
                  title: '没有更多图片了...',
                  icon: 'none'
                });
          return
        }
        // 判断是否是初始化页面的请求
        if (this.recommendList.length === 0) {
          // 推荐模块
          this.recommendList = res.data.res.homepage[1].items
          // 月份模块
          this.monthes = res.data.res.homepage[2]
          // 转换时间戳
          this.monthes.MM = moment(this.monthes.stime).format('MM')
          this.monthes.DD = moment(this.monthes.stime).format('DD')
        }
        
        // 获取热门数据 -> 拼接
        this.hots = [...this.hots, ...res.data.res.vertical]
      })
    },
    handleToLower () {
      // 判断是否还有数据可以加载
      if (this.hasMore) {
        // 1.改变请求参数
        this.params.skip += this.params.limit
        // 2.请求
        this.getHomeRecommendData()
      }else {
        uni.showToast({
          title: '没有更多图片了...',
          icon: 'none'
        })
      }
    }
  },
  mounted () {
    this.getHomeRecommendData()
    uni.setNavigationBarTitle({
      title: '推荐'
    });
  }
}
</script>

<style lang="scss">
.recommend-view {
  height: calc( 100vh - 36px);
}

.recommend-wrap {
  display: flex;
  flex-wrap: wrap;
  .recommend-items {
    width: 50%;
    // hack: 用白边框撑margin
    border: 5rpx solid #fff;
  }
}

.month-wrap {
  .month-title {
    display: flex;
    justify-content: space-between;
    padding: 20rpx;
    .month-title-info {
      font-weight: 600;
      display: flex;
      .month-info {
        color: $color;
        text {
          font-size: 36rpx;
        }
      }
      .month-text {
        margin-left: 20rpx;
        color: #666;
        font-size: 34rpx;
        
      }
    }

    .month-title-more {
      color: $color;
    }
  }

  .month-content {
    display: flex;
    flex-wrap: wrap;
    .month-content-item {
      width: 33.33%;
      border: 5rpx solid #fff;
    }
  }
}

.hots_wrap {
  .hots_title {
    padding: 20rpx;
    text {
      border-left: 5rpx solid $color;
      font-size: 36rpx;
      font-weight: 600;
      padding-left: 20rpx;
    }
  }

  .hots_content {
    display: flex;
    flex-wrap: wrap;
    .hot_item {
      width: 33.33%;
      border: 5rpx solid #fff;
      image {

      }
    }
  }
}
</style>