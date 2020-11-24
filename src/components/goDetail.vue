<template>
  <view @click="handleClick">
    <slot></slot>
  </view>
</template>

<script>
export default {
  name: 'goDetail',
  props: {
    list: {
      type: Array,
      default: []
    },
    index: {
      type: Number,
      default: 0
    }
  },
  methods: {
    handleClick () {
      // 1.缓存数据
      getApp().globalData.imgList = this.list
      getApp().globalData.imgIndex = this.index
      // 2.页面跳转
      wx.navigateTo({
        url: '/pages/imgDetail/index'
      });
    }
  }
}
</script>

<style lang="scss" scoped>

</style>