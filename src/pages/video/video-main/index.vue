<template>
  <scroll-view class="video-main" scroll-y enable-flex @scrolltolower="handleScrollToLower">
    <view class="video-item" v-for="item of videowp" :key="item.id" @click="handleClick(item)">
      <image :src="item.img" mode="widthFix" />
    </view>
  </scroll-view>
</template>

<script>
export default {
  data () {
    return {
      videowp: [],
      hasMore: true
    }
  },
  props: {
    urlobj: {
      type: Object,
      default: {}
    }
  },
  watch: {
    urlobj () {
      this.videowp = []
      this.getData()
    },
  },
  mounted () {
    this.getData()
  },
  methods: {
    getData () {
      this.request({
        url: this.urlobj.url,
        data: this.urlobj.params
      })
      .then(res => {
        if (res.data.res.videowp.length === 0) {
          this.hasMore = false
          uni.showToast({
            title: '没有更多数据了...',
            icon: 'none'
          })
          return
        }
        this.videowp = [...this.videowp, ...res.data.res.videowp]
      })
    },
    handleScrollToLower () {
      if (this.hasMore) {
        this.urlobj.params.skip += this.urlobj.params.limit
        this.getData()
      }else {
        uni.showToast({
          title: '没有更多数据了...',
          icon: 'none'
        })
      }
    },
    handleClick (item) {
      getApp().globalData.video = item
      uni.navigateTo({
         url: '/pages/videoPlay/index'
      })
    }
  }
}
</script>

<style lang="scss" scoped>
.video-main {
  display: flex;
  flex-wrap: wrap;
  height: calc(100vh - 36px);
  .video-item {
    width: 33.33%;
    border: 5rpx solid #fff;
  }
}


</style>