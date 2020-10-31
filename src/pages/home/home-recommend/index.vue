<template>
  <view>
    <!-- 推荐 开始 -->
    <view class="recommend_wrap">
      <view class="recommend_items" v-for="item in recommendList" :key="item.id">
        <image mode="widthFix" :src="item.thumb"/>
      </view>
    </view>
    <!-- 推荐 结束 -->

  </view>
</template>

<script>
export default {
  data () {
    return {
      recommendList: []
    }
  },
  methods: {
    getHomeRecommendData (params) {
      this.request(params).then( (res) => {
        console.log(res);
        this.recommendList = res.data.res.homepage[1].items
        console.log(this.recommendList);
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
.recommend_wrap {
  display: flex;
  flex-wrap: wrap;
  .recommend_items {
    width: 50%;
    // hack: 用白边框撑margin
    border: 5rpx solid #fff;
  }
}
</style>