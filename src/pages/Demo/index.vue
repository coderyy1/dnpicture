<template>
  <view @touchstart="handleStart" @touchend="handleEnd">
    1
  </view>
</template>

<script>
export default {
  data () {
    return {
      // 按下的时间
      startTime: 0,
      // 按下的坐标
      startX: 0
    }
  },
  methods: {
    handleStart (e) {
      console.log('按下', e.changedTouches[0].clientX);
      this.startTime = Date.now()
      this.startX = e.changedTouches[0].clientX
    },
    handleEnd (e) {
      console.log('松开', e.changedTouches[0].clientX);
      const endTime = Date.now()
      const endX = e.changedTouches[0].clientX

      // 处理
      let direction = ''

      if (endTime - this.startTime > 8000) {
        return 
      }
      if (Math.abs(endX - this.startX) > 22) {
        direction = endX - this.startX > 0 ? 'right' : 'left'
        console.log(direction);
      }else {
        return
      }
    }
  }
}
</script>

<style lang="scss" scoped>
view {
  height: 600rpx;
  width: 100%;
  background-color: #f00;
}
</style>