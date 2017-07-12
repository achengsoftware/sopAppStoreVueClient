<template lang="pug">
.app-box
  .content-box
    SwiperBox
    AppPlate
  BottomBar(index="1")
</template>

<script>
import AppPlate from './app/AppPlate'
import SwiperBox from './app/SwiperBox'
import BottomBar from './brick/Bottom'

export default {
  components: {
    BottomBar,
    AppPlate,
    SwiperBox
  }
}
</script>

<style lang='less' scoped>
.content-box {
  height: calc(~"100% - 50px");
}
</style>
