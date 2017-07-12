<template lang="pug">
  router-link.panel42(:to="url",:class="{ isIcon: icoCode }")
    .ico.icon(v-if="icoCode",v-html="icoCode",:style="'color:'+color")
    .text {{text}}
    .ico.enter &#xe659;
</template>

<script>
export default {
  props: {
    icoCode: String,
    text: String,
    url:String,
    color: String
  }
}
</script>

<style lang='less' scoped>
.panel42{
    display: flex;
    height: 42px;
    background-color: rgba(255, 255, 255, 0.7);
    border-bottom: 1px solid #eaeaea;
    .ico{
        width: 42px;
        height: 42px;
        text-align: center;
        line-height: 42px;
        color:#a4a9b2;
    }
    .icon{
        font-size: 1.2rem;
    }
    .text{
        width: calc(~"100% - 84px");
        line-height: 42px;
        font-size: 0.9rem;
        margin: 0 21px;
    }
}
.panel42:active{
    background-color: aqua;
}
.isIcon{
    .text{
        margin: 0;
    }
}
</style>