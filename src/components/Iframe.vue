<template lang="pug">
.iframe-box
  TitleBar(:title='$route.params.name')
  iframe#iframe(:src="iframeURL",frameborder="0")
  img.load(v-if="!ok",:src="imgPath")
</template>

<script>
import TitleBar from './brick/Title'
import { DATA } from "./method.js"
export default {
  components: {
    TitleBar
  },
  activated () {
    this.ok = false
    this.iframeURL = DATA.iframeURL
    let iframe = document.getElementById("iframe");
    iframe.onload = (e) => {
      this.ok = true
    }
  },
  data () {
    return {
      iframeURL: '',
      ok: false,
      imgPath: require("../assets/ring.gif")
    }
  }
}
</script>
<style lang='less' scoped>
.iframe-box {
  position: absolute;
  top: 0;
  left: 0;
  height: 525px;
  width: 100%;
  iframe {
    height: 100%;
    width: 100%;
  }
}
</style>