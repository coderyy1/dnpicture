<template>
  <view>
    <!-- 专辑背景 开始 -->
    <view class="album-bg">
      <image :src="album.cover" mode="widthFix" />
      <view class="bg-info">
        <view class="info-title">{{album.name}}</view>
        <view class="info-btn">关注专辑</view>
      </view>
    </view>
    <!-- 专辑背景 结束 -->

    <!-- 专辑作者 开始 -->
    <view class="album-author">
      <view class="author-info">
        <image class="info-icon" :src="album.user.avatar" mode="widthFix"></image>
        <view class="info-name">{{album.user.name}}</view>
      </view>
      <view class="author-desc"><text>{{album.desc}}</text></view>
    </view>
    <!-- 专辑作者 结束 -->

    <!-- 列表 开始 -->
    <view class="album-list">
      <view class="album-item" v-for="(item, index) of wallpaper" :key="item.id">
        <go-detail :list="wallpaper" :index="index">
          <image :src="item.thumb + item.rule.replace('$<Height>', 360)" mode="aspectFill" />
        </go-detail>
      </view>
    </view>
    <!-- 列表 结束 -->
  </view>
</template>

<script>
import goDetail from '@/components/goDetail'

export default {
  components: {
    goDetail
  },
  data () {
    return {
      params: {
        limit: 30,
        order: 'new',
        skip: 0,
        first: 1
      },
      id: -1,
      album: {},
      wallpaper: [],
      hasMore: true
    }
  },
  onLoad (options) {
    this.id = options.id
    this.getList()
  },
  onReachBottom () {
    if (this.hasMore) {
      this.params.skip += this.params.limit
      this.getList() 
    }else {
      uni.showToast({
        title: '没有更多数据了...',
        icon: 'none'
      });
    }
    
  },
  methods: {
    getList () {
      this.request({
        url: `http://157.122.54.189:9088/image/v1/wallpaper/album/${this.id}/wallpaper`,
        data: this.params
      })
      .then(res => {
        if (res.data.res.wallpaper.length === 0) {
          this.hasMore = false
          uni.showToast({
            title: '没有更多数据了...',
            icon: 'none'
          });
          return
        }
        if (this.params.first === 1) {
          this.album = res.data.res.album
          this.params.first = 0
        }
        this.wallpaper = [...this.wallpaper, ...res.data.res.wallpaper]
      })
    }
  }
}
</script>

<style lang="scss" scoped>
  .album-bg {
    position: relative;
    image {

    }

    .bg-info {
      position: absolute;
      display: flex;
      justify-content: space-between;
      align-items: center;
      width: 100%;
      padding: 20rpx 30rpx;
      left: 0;
      bottom: 0;
      .info-title {
        font-size: 40rpx;
        color: #fff;
      }

      .info-btn {
        padding: 8rpx 16rpx;
        font-size: 26rpx;
        color: #fff;
        background-color: $color;
        border-radius: 8rpx;
      }
    }
  }

  .album-author {
    padding: 20rpx;
    .author-info {
      display: flex;
      margin-bottom: 26rpx;
      .info-icon {
        width: 96rpx;
        height: 96rpx;
        box-shadow: 0 0 2px 1px rgba(100, 100, 100, .4);
        
        border-radius: 50%;
      }

      .info-name {
        margin-left: 20rpx;
        color: #000;
        font-size: 40rpx;
        font-weight: 600;
      }
    }

    .author-desc {
      color: #000;
    }
  }

  .album-list {
    display: flex;
    flex-wrap: wrap;
    .album-item {
      width: 50%;
      border: 3rpx solid #fff;
      image {
        height: 160rpx;
      }
    }
  }
</style>