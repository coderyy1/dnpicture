<template>
  <view v-if="isDataReady">
    <!-- 推荐 开始 -->
    <view class="recommend-wrap">
      <view class="recommend-items" v-for="item in recommendList" :key="item.id">
        <image mode="widthFix" :src="item.thumb"/>
      </view>
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
        <view class="month-content-item" v-for="item of monthes.items" :key="item.id">
          <image mode="aspectFill" :src="item.img + item.rule.replace('$<Height>', 360)"/>
        </view>
      </view>
    </view>
    <!-- 月份 结束 -->
  </view>
</template>

<script>
import moment from 'moment'

export default {
  data () {
    return {
      // 推荐列表
      recommendList: [],
      // 月份
      monthes: {}
    }
  },
  computed: {
    isDataReady () {
      return this.recommendList.length
    }
  },
  methods: {
    getHomeRecommendData (params) {
      this.request(params).then( (res) => {
        // 推荐模块
        this.recommendList = res.data.res.homepage[1].items
        // 月份模块
        this.monthes = res.data.res.homepage[2]
        // 转换时间戳
        this.monthes.MM = moment(this.monthes.stime).format("MM")
        this.monthes.DD = moment(this.monthes.stime).format("DD")
        console.log(this.monthes);
      })
    }
  },
  mounted () {
    this.getHomeRecommendData ({
      url: "http://157.122.54.189:9088/image/v3/homepage/vertical",
      data: {
        limit: 30,
        order: 'hot',
        skip: 0
      }
    })
  }
}
</script>

<style lang="scss">
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
  height: 58rpx;
  line-height: 58rpx;
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
</style>