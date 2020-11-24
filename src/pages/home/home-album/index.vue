<template>
  <scroll-view class="album-scroll" scroll-y @scrolltolower="handleToLower" v-if="isReady">
    <!-- 轮播图 开始 -->
      <view class="album-swiper">
        <swiper  autoplay indicator-dots circular>
          <swiper-item v-for="item of banner" :key="item.id">
            <image :src="item.thumb" mode="widthFix" />
          </swiper-item>
        </swiper>
      </view>
    <!-- 轮播图 结束 -->

    <!-- 列表 开始 -->
      <view class="album-list">
        <navigator 
            class="album-item" 
            v-for="item of album" 
            :key="item.id" 
            :url="`/pages/album/index?id=${item.id}`"
        >
          <view class="item-img">
            <image mode="aspectFill" :src="item.cover" />
          </view>
          <view class="item-info">
            <view class="info-title">{{item.name}}</view>
            <view class="desc">{{item.desc}}</view>
            <view class="info-btn">
              <view class="btn-attention">
                + 关注
              </view>
            </view>
          </view>
        </navigator>
      </view>
    <!-- 列表 结束 -->
  </scroll-view>
</template>

<script>
export default {
  data() {
    return {
      params: {
        limit: 30,
        order: 'new',
        skip: 0
      },
      // 轮播图数组
      banner: [],
      // 列表数组
      album: [],
      // 是否还有数据
      hasMore: true
    }
  },
  computed: {
    isReady () {
      return this.banner.length || this.album.length
    }
  },
  methods: {
    // 获取数据
    getList () {
      this.request({
        url: 'http://157.122.54.189:9088/image/v1/wallpaper/album',
        data: this.params
      })
      .then(res => {
        if (res.data.res.album.length === 0) {
          this.hasMore = false
          uni.showToast({
                  title: '没有更多数据了...',
                  icon: 'none'
                });
          return
        }
        if (this.banner.length === 0) {
          this.banner = res.data.res.banner
        }
        this.album = [...this.album, ...res.data.res.album]
      })
    },
  handleToLower () {
    if (this.hasMore) {
      this.params.skip += this.params.limit 
      this.getList()
    }else {
      uni.showToast({
        title: '没有更多数据了...',
        icon: 'none'
      });
    }
  }
  },
  mounted () {
    this.getList()
    uni.setNavigationBarTitle({
      title: '专辑'
    });
  }
}
</script>

<style lang="scss" scoped>

  .album-scroll {
    height: calc( 100vh - 36px);
  }

  .album-swiper {
    swiper {
      height: calc(750rpx / 2.3);
    }
  }

  .album-list {
    padding: 10rpx;
  .album-item {
    display: flex;
    padding: 10rpx 0;
    border-bottom: 1rpx solid #ccc;
    .item-img {
      flex: 1;
      padding: 10rpx;
      image {
        width: 200rpx;
        height: 200rpx;
      }
    }

    .item-info {
      flex: 2;
      padding: 0 10rpx;
      overflow: hidden;
      .info-title {
        font-size: 32rpx;
        color: #000;
        padding: 10rpx 0;
      }

      .desc {
        font-size: 24rpx;
        padding: 10rpx 0;
        overflow: hidden;
        text-overflow: ellipsis;
        white-space: nowrap;
      }

      .info-btn {
        float: right;
        .btn-attention {
          padding: 10rpx;
          border: 1rpx solid $color;
          color: $color;
        }
      }
    }
  }
}
</style>