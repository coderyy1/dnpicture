<template>
  <view @touchstart="handleStart" @touchend="handleEnd">
    <slot></slot>
  </view>
</template>

<script>
export default {
  name: 'swiperAction',
  data () {
    return {
      // 按下的时间
      startTime: 0,
      // 按下的坐标
      startX: 0,
      startY: 0
    }
  },
  methods: {
    handleStart (e) {
      this.startTime = Date.now()
      this.startX = e.changedTouches[0].clientX
      this.startY = e.changedTouches[0].clientY
    },
    handleEnd (e) {
      const endTime = Date.now()
      const endX = e.changedTouches[0].clientX
      const endY = e.changedTouches[0].clientY

      // 处理
      let direction = ''

      if (endTime - this.startTime > 8000) {
        return 
      }
      if (Math.abs(endX - this.startX) > 22 && Math.abs(endY - this.startY) < 22) {
        direction = endX - this.startX > 0 ? 'right' : 'left'
        // console.log(direction);
      }else {
        return
      }

      this.$emit('handleSwiper', direction)
    }
  }
}
</script>

<style lang="scss" scoped>

</style>