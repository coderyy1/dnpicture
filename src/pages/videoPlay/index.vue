<template>
  <view class="video-play">
    <image :src="videoObj.img" mode="" />
    <!-- 工具栏 开始 -->
    <view class="tool-bar">
      <view :class="['iconfont', muted?'iconjingyin':'iconshengyin']" @click="mutedClick"></view>
      <button open-type="share" class="iconfont iconzhuanfa"></button>
    </view>
    <!-- 工具栏 结束 -->

    <!-- 视频 开始 -->
    <view class="video-wrap">
      <video :src="videoObj.video" objectFit="fill" :muted="muted"></video>
    </view>
    <!-- 视频 结束 -->

    <!-- 下载 开始 -->
    <view class="download">
      <view class="download-btn" @click="download">下载高清</view>
    </view>
    <!-- 下载 结束 -->
  </view>
</template>

<script>
export default {
  data () {
    return {
      videoObj: {},
      muted: false
    }
  },
  onLoad() {
    console.log(getApp().globalData);
    this.videoObj = getApp().globalData.video
  },
  methods: {
    mutedClick () {
      this.muted = !this.muted
    },
  async download () {
    await uni.showLoading({
      title: '下载中'
    })
    const {tempFilePath} = (await uni.downloadFile({url: this.videoObj.video}))[1]
    await uni.saveVideoToPhotosAlbum({filePath: tempFilePath})
    uni.hideLoading()
    await uni.showToast({
      title: '下载成功！',
      duration: 2000
    })
    }
  }
}
</script>

<style lang="scss" scoped>
.video-play {
  position: relative;
  image { 
    position: absolute;
    width: 100vw;
    height: 100vh;
    z-index: -1;
    // c3滤镜
    filter: blur(20px)
  }
  .tool-bar {
    display: flex;
    height: 80rpx;
    justify-content: flex-end;
    button {
      padding: 0;
      margin: 0;
    }
    .iconfont {
      display: flex;
      justify-content: center;
      align-items: center;
      width: 80rpx;
      margin-right: 20rpx;
      color: #fff;
      font-size: 50rpx;
      border-radius: 40rpx;
      background-color: rgba(0, 0, 0, .2);
    }
  }

  .video-wrap {
    display: flex;
    justify-content: center;
    video {
      width: 360rpx;
      height: 600rpx;
    }
  }

  .download {
    display: flex;
    justify-content: center;
    .download-btn {
      display: flex;
      justify-content: center;
      align-items: center;
      width: 360rpx;
      height: 80rpx;
      margin-top: 20rpx;
      color: #fff;
      border-radius: 40rpx;
      border: 1rpx solid #fff;
      background-color: rgba(0, 0, 0, .4);
    }
  }
}
</style>