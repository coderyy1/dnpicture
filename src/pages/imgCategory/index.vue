<template>
  <view>
    <view class="cate-tab">
      <view class="cate-tab-header">
        <view class="header-inner">
          <uni-segmented-control :current="current" :values="items.map(v=>v.title)" @clickItem="onClickItem" style-type="text" active-color="#d4237a">
          </uni-segmented-control>
        </view>
        <view class="iconfont iconsearch"></view>
      </view>
      <scroll-view 
        class="cate-tab-content" 
        @scrolltolower="handleToLower" 
        scroll-y 
        v-if="isReady"
      >
        <view class="item-wrap">
          <view class="cate-item" v-for="item of list" :key="item.id">
            <image :src="item.thumb" mode="widthFix" />
          </view>
        </view>
      </scroll-view>
      <view class="desc" v-else>没有图片了....</view>
    </view>
  </view>
</template>

<script>
import {uniSegmentedControl} from '@dcloudio/uni-ui'

export default {
  components: {
    uniSegmentedControl
  },
  onLoad (options) {
    this.id = options.id
    this.getList()
  },
  data () {
    return {
      items: [
        {title: '最新', order: 'new'},
        {title: '热门', order: 'hot'}
      ],
      current: 0,
      params: {
        limit: 30,
        skip: 0,
        order: 'new'
      },
      id: -1,
      list: [],
      hasMore: true
    }
  },
  computed: {
    isReady () {
      return this.list.length
    }
  },
  methods: {
    onClickItem (e) {
      if (this.current !== e.currentIndex) {
        this.current = e.currentIndex
        this.params.order = this.items[e.currentIndex].order
        this.list = []
        this.params.skip = 0
        this.getList()
      }
    },
    getList () {
      this.request({
        url: `http://157.122.54.189:9088/image/v1/vertical/category/${this.id}/vertical`,
        data: this.params
      })
      .then(res => {
        if (res.data.res.vertical.length === 0) {
          this.hasMore = false
          uni.showToast({
            title: '没有更多图片了...',
            icon: 'none'
          });
          return
        }
        this.list = [...this.list, ...res.data.res.vertical]
      })
    },
    handleToLower () {
      if (this.hasMore) {
        this.params.skip += this.params.limit
        this.getList()
      }else {
        uni.showToast({
          title: '没有更多图片了...',
          icon: 'none'
        });
      }
    }
  }
}
</script>

<style lang="scss" scoped>
.cate-tab {
  .cate-tab-header {
    position: relative;
    .header-inner {
      width: 60%;
      margin: 0 auto;
    }
    .iconsearch {
      position: absolute;
      top: 50%;
      transform: translateY(-50%);
      right: 5%;
    }
  }
  .cate-tab-content {
    height: calc(100vh - 36px);
    .item-wrap {
      display: flex;
      flex-wrap: wrap;
      .cate-item {
        width: 33.33%;
        border: 5rpx solid #fff;
      }
    }
  }
  .desc {
    position: relative;
    top: 60rpx;
    text-align: center;
    font-size: 44rpx;
    color: #000;
  }
}
</style>