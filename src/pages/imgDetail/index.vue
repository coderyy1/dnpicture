<template>
  <view>
    <!-- 用户信息 开始 -->
    <view class="user" v-if="imgDetail.user">
      <image class="u-icon" :src="imgDetail.user.avatar" mode="widthFix" ></image>
      <view class="u-info">
        <view class="u-info-name">{{imgDetail.user.name}}</view>
        <view class="u-info-time">{{imgDetail.cnTime}}</view>
      </view>
    </view>
    <!-- 用户信息 结束 -->

    <!-- 大图 开始 -->
    <view class="bigimg">
      <swiper-action @handleSwiper="fatherSwiper">
        <image :src="imgDetail.thumb" mode="" />
      </swiper-action>
    </view>
    <!-- 大图 结束 -->

    <!-- 点赞 开始 -->
    <view class="pick">
      <view class="pick-good">
        <text class="iconfont icondianzan">{{imgDetail.rank}}</text>
      </view>
      <view class="pick-ifav">
        <text class="iconfont iconshoucang">收藏</text>
      </view>
    </view>
    <!-- 点赞 结束 -->

    <!-- 下载图片 开始 -->
    <view class="download">
      <view class="download-btn" @click="downloadClick">下载图片</view>
    </view>
    <!-- 下载图片 结束 -->

    <!-- 最热评论 开始 -->
    <view class="comment hot" v-if="hot.length">
      <view class="comment-title">
        <text class="iconfont iconhot1"></text>
        <text class="comment-text">最热评论</text>
      </view>
      <view class="comment-list">
        <view class="comment-item" v-for="item of hot" :key="item.id">
          <view class="comment-user">
            <view class="user-icon">
              <image mode="widthFix" :src="item.user.avatar"/>
            </view>
            <view class="user-info">
              <view class="info-name">{{item.user.name}}</view>
              <view class="info-time">{{item.cnTime}}</view>
            </view>
            <view class="user-badge">
              <image :src="bitem.icon" mode="" v-for="bitem of item.user.title" :key="bitem.icon"/>
            </view>
          </view>
          <view class="comment-desc">
            <view class="desc-content">
              <text class="content">{{item.content}}</text>
            </view>
            <view class="desc-pick">
              <text class="iconfont icondianzan">{{item.size}}</text>
            </view>
          </view>
        </view>
      </view>
    </view>
    <!-- 最热评论 结束 -->

    <!-- 最新评论 开始 -->
    <view class="comment new" v-if="comment.length">
      <view class="comment-title">
        <text class="iconfont iconpinglun"></text>
        <text class="comment-text">最新评论</text>
      </view>
      <view class="comment-list">
        <view class="comment-item" v-for="item of comment" :key="item.id">
          <view class="comment-user">
            <view class="user-icon">
              <image mode="widthFix" :src="item.user.avatar"/>
            </view>
            <view class="user-info">
              <view class="info-name">{{item.user.name}}</view>
              <view class="info-time">{{item.cnTime}}</view>
            </view>
            <view class="user-badge">
              <image :src="bitem.icon" mode="" v-for="bitem of item.user.title" :key="bitem.icon"/>
            </view>
          </view>
          <view class="comment-desc">
            <view class="desc-content">
              <text class="content">{{item.content}}</text>
            </view>
            <view class="desc-pick">
              <text class="iconfont icondianzan">{{item.size}}</text>
            </view>
          </view>
        </view>
      </view>
    </view>
    <!-- 最新评论 结束 -->
  </view>
</template>

<script>
import moment from 'moment'

import swiperAction from '@/components/swiperAction'
// 设置中文
moment.locale("zh-cn")

export default {
  components: {
    swiperAction
  },
  data () {
    return {
      imgDetail: {},
      hot: [],
      comment: [],
      imgIndex: 0,
      imgList: []
    }
  },
  onLoad () {
    const {imgList, imgIndex} = getApp().globalData
    this.imgIndex = imgIndex
    this.imgList = imgList
    this.imgDetail = this.imgList[this.imgIndex]
    this.getData()
  },
  methods: {
    getData () {
      this.request({
        url: `http://157.122.54.189:9088/image/v2/wallpaper/wallpaper/${this.imgDetail.id}/comment`
      })
      .then(res => {
        // 时间
        this.imgDetail.cnTime = moment(this.imgDetail.atime * 1000).fromNow()
        
        // 添加时间
        res.data.res.hot.forEach(v => {v.cnTime = moment(v.atime * 1000).fromNow()})
        res.data.res.comment.forEach(v => {v.cnTime = moment(v.atime * 1000).fromNow()})

        this.hot = res.data.res.hot
        this.comment = res.data.res.comment
      })
    },
    fatherSwiper (direction) {
      if (direction === 'left') {
        // 左划 -> 下一张
        if (this.imgIndex !== this.imgList.length - 1) {
          this.imgIndex ++
          this.imgDetail = this.imgList[this.imgIndex]
          this.getData()
        }else {
          uni.showToast({
            title: '没有下一张了...',
            icon: 'none'
          });
        }
      }else if (direction === 'right'){
        // 右滑 -> 上一张
        if (this.imgIndex !== 0) {
          this.imgIndex --
          this.imgDetail = this.imgList[this.imgIndex]
          this.getData()
        }else {
          uni.showToast({
            title: '没有上一张了...',
            icon: 'none'
          });
        }
      }
    },
    async downloadClick () {

      await uni.showLoading({
        title: '下载中'
      })

      // 1.将图片下载至小程序内存
      const res1 = await uni.downloadFile({url: this.imgDetail.img})
      const {tempFilePath} = res1[1]

      // 2.将小程序内存中的文件下载到本地上
      const res2 = await uni.saveImageToPhotosAlbum({filePath: tempFilePath})

      uni.hideLoading();

      await uni.showToast({
        title: '下载成功！',
        duration: 2000
      });
    }
  }
}
</script>

<style lang="scss" scoped>
.user {
  display: flex;
  align-items: center;
  padding: 20rpx 40rpx;
  .u-icon {
    width: 88rpx;
    border-radius: 50%;
    box-shadow: 0 0 4px 2px #ccc;
  }

  .u-info {
    margin-left: 28rpx;
    .u-info-name {
      color: #000;
      font-size: 28rpx;
      font-weight: 600;
    }

    .u-info-time {
      color: #ccc;
      font-size: 20rpx;
    }
  }
}

.pick {
  display: flex;
  height: 80rpx;
  border-bottom: 5rpx solid #eaeaea;
  .pick-good {
    display: flex;
    justify-content: center;
    align-items: center;
    flex: 1;
    .iconfont.icondianzan {
      font-size: 26rpx;
    }
  }

  .pick-ifav {
    display: flex;
    justify-content: center;
    align-items: center;
    flex: 1;
    .iconfont.iconshoucang {
      font-size: 26rpx;
    }
  }
}

.bigimg {
  image {
    border-bottom: 1rpx solid #ccc;
  }
}

.comment {
  .comment-title {
    display: flex;
    align-items: center;
    padding: 15rpx;
    .iconfont {
      color: #f00;
      font-size: 40rpx;
    }

    .comment-text {
      font-weight: 600;
      font-size: 28rpx;
      color: #666;
      margin-left: 10rpx;
    }
  }

  .comment-list {
  .comment-item {
    border-bottom: 5rpx solid #eaeaea;
    padding: 18rpx;
    // 用户信息
    .comment-user {
      display: flex;
      .user-icon {
        width: 10%;
        display: flex;
        justify-content: center;
        align-items: center;
        margin-right: 16rpx;
        image {
          width: 100%;
        }
      }

      .user-info {
        flex: 1;
        .info-name {
          color: #777;

        }

        .info-time {
          color: #333;
          font-size: 24rpx;
          padding: 5rpx 0;
        }
      }

      .user-badge {
        display: flex;
        image {
          width: 40rpx;
          height: 40rpx;
        }
      }
    }

    .comment-desc {
      display: flex;
      padding: 40rpx 16rpx;
      .desc-content {
        flex: 1;
        padding-left: 10%;
        padding-right: 10%;
        .content {
          color: #000;
          font-weight: 600;
        }
      }

      .desc-pick {
        display: flex;
        align-items: center;
        text-align: right;
        .iconfont {

        }
      }
    }
  }
}
}

.hot {
  border-top: 32rpx solid #eaeaea;
  box-shadow: 0 -4px 6px 0 #eaeaea,
               0 4px 6px 0 #eaeaea;
}


.new {
  border-top: 32rpx solid #eaeaea;
  box-shadow: 0 -4px 6px 0 #eaeaea,
               0 4px 6px 0 #eaeaea;
  .comment-title {
    .iconfont {
      color: blue;
    }
  }
}

// 下载图片
.download {
  height: 120rpx;
  width: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
  .download-btn {
    display: flex;
    justify-content: center;
    align-items: center;
    width: 90%;
    height: 80%;
    font-size: 46rpx;
    font-weight: 600;
    background-color: $color;
    color: #fff;
    
  }
}
</style>