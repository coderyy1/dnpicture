<template>
  <scroll-view class="home-category" scroll-y v-if="isReady">
    <view class="cate-wrap">
      <navigator 
        class="cate-item" 
        v-for="item of list" 
        :key="item.id" 
        :url="`/pages/imgCategory/index?id=${item.id}`"
      >
        <image :src="item.cover" mode="aspectFill" />
        <view class="cate-name">{{item.name}}</view>
      </navigator>
    </view>
  </scroll-view>
</template>

<script>
export default {
  data () {
    return {
      list: []
    }
  },
  computed: {
    isReady () {
      return this.list.length
    }
  },
  mounted () {
  uni.setNavigationBarTitle({
    title: '分类'
  })
  this.getData()
  },
  methods: {
    getData () {
      this.request({
        url: 'http://157.122.54.189:9088/image/v1/vertical/category'
      })
      .then(res => {
        this.list = res.data.res.category
      })
    }
  }
}
</script>

<style lang="scss" scoped>
.home-category {
  height: calc( 100vh - 36px);
  .cate-wrap {
    display: flex;
    flex-wrap: wrap;
    .cate-item {
    position: relative;
    width: 33.33%;
    border: 5rpx solid #fff;
      image {
        height: 240rpx;
      }

      .cate-name {
        position: absolute;
        bottom: 0;
        left: 0;
        right: 0;
        padding-left: 8rpx;
        font-size: 36rpx;
        color: #fff;
        background-image: linear-gradient(to right top, rgba(0, 0, 0, .2), rgba(0, 0, 0, 0));
      }
    }
  }
}

</style>