<template lang="pug">
.version
    TitleBar(title='帮助',leftIcon="flase")
    .ver 暂时没有内容
</template>

<script>
import TitleBar from '../brick/Title'
export default {
  components: {
    TitleBar
  }
}
</script>

<style lang='less' scoped>
.version{
    .ver{
        text-align: center;
        height: 500px;
        line-height: 500px;
        font-size: 1.4rem;
        color: #ccc;
    }
}
</style>
