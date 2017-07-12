<template lang="pug">
  .title-bar
    .titleButton.ico(v-bind:class="{ hidden: !leftIcon }",v-on:click="back") &#xe601;
    p.title {{title}}
    router-link.titleButton.ico(tag="div",v-if="rightIcon === 'add'",to="\Store") &#xe655;
    .titleButton(v-if="rightIcon === 'save'",v-on:click="save") 保存
    router-link.titleButton.ico(tag="div",v-if="rightIcon === 'search'",to="\Search") &#xe60b;
    .loading(v-if="rightIcon === 'loading'")
      <svg width='120px' height='120px' xmlns="http://www.w3.org/2000/svg" viewBox="0 0 100 100" preserveAspectRatio="xMidYMid" class="uil-default">
        <rect x="0" y="0" width="100" height="100" fill="none" class="bk"></rect>
        <rect  x='46.5' y='40' width='7' height='20' rx='5' ry='5'  transform='rotate(0 50 50) translate(0 -30)'>  
          <animate attributeName='opacity' from='1' to='0' dur='1s' begin='-1s' repeatCount='indefinite'/>
        </rect>
        <rect  x='46.5' y='40' width='7' height='20' rx='5' ry='5'  transform='rotate(30 50 50) translate(0 -30)'>  
          <animate attributeName='opacity' from='1' to='0' dur='1s' begin='-0.9166666666666666s' repeatCount='indefinite'/>
        </rect>
        <rect  x='46.5' y='40' width='7' height='20' rx='5' ry='5'  transform='rotate(60 50 50) translate(0 -30)'>  
          <animate attributeName='opacity' from='1' to='0' dur='1s' begin='-0.8333333333333334s' repeatCount='indefinite'/>
        </rect>
        <rect  x='46.5' y='40' width='7' height='20' rx='5' ry='5'  transform='rotate(90 50 50) translate(0 -30)'>  
          <animate attributeName='opacity' from='1' to='0' dur='1s' begin='-0.75s' repeatCount='indefinite'/>
        </rect>
        <rect  x='46.5' y='40' width='7' height='20' rx='5' ry='5'  transform='rotate(120 50 50) translate(0 -30)'>  
          <animate attributeName='opacity' from='1' to='0' dur='1s' begin='-0.6666666666666666s' repeatCount='indefinite'/>
        </rect>
        <rect  x='46.5' y='40' width='7' height='20' rx='5' ry='5'  transform='rotate(150 50 50) translate(0 -30)'>  
          <animate attributeName='opacity' from='1' to='0' dur='1s' begin='-0.5833333333333334s' repeatCount='indefinite'/>
        </rect>
        <rect  x='46.5' y='40' width='7' height='20' rx='5' ry='5'  transform='rotate(180 50 50) translate(0 -30)'>  
          <animate attributeName='opacity' from='1' to='0' dur='1s' begin='-0.5s' repeatCount='indefinite'/>
        </rect>
        <rect  x='46.5' y='40' width='7' height='20' rx='5' ry='5'  transform='rotate(210 50 50) translate(0 -30)'>  
          <animate attributeName='opacity' from='1' to='0' dur='1s' begin='-0.4166666666666667s' repeatCount='indefinite'/>
        </rect>
        <rect  x='46.5' y='40' width='7' height='20' rx='5' ry='5'  transform='rotate(240 50 50) translate(0 -30)'>  
          <animate attributeName='opacity' from='1' to='0' dur='1s' begin='-0.3333333333333333s' repeatCount='indefinite'/>
        </rect>
        <rect  x='46.5' y='40' width='7' height='20' rx='5' ry='5'  transform='rotate(270 50 50) translate(0 -30)'>  
          <animate attributeName='opacity' from='1' to='0' dur='1s' begin='-0.25s' repeatCount='indefinite'/>
        </rect>
        <rect  x='46.5' y='40' width='7' height='20' rx='5' ry='5'  transform='rotate(300 50 50) translate(0 -30)'>  
          <animate attributeName='opacity' from='1' to='0' dur='1s' begin='-0.16666666666666666s' repeatCount='indefinite'/>
        </rect>
        <rect  x='46.5' y='40' width='7' height='20' rx='5' ry='5'  transform='rotate(330 50 50) translate(0 -30)'>  
          <animate attributeName='opacity' from='1' to='0' dur='1s' begin='-0.08333333333333333s' repeatCount='indefinite'/>
        </rect>
      </svg>
</template>

<script>
import { Order } from '../Order.js'
export default {
  props: {
    title: { type: String, required: false, default: '标题栏'},
    leftIcon: String,
    rightIcon: String
  },
  methods: {
    back () {
      history.go(-1);
    },
    save () {
      Order.$emit('TITLEBUTTONCLICK', 'save')
    }
  },
}

</script>

<style lang='less' scoped>
/*标题栏样式*/
.title-bar {
  background-color: #f8f8f8;
  height:44px;
  line-height:44px;
  width:100%;
  text-align:center;
  border-bottom: 1px solid #dfdde8;
  display: flex;
  z-index: 99;
  top: 0;
  .title{
    height: 45px;
    line-height: 45px;
    font-size: 18px;
    font-family: -apple-system-font,Helvetica Neue,sans-serif;
    width: calc(~"100% - 90px");
    margin: 0;
  }
  /*标题栏按钮样式*/
  .titleButton{
    height: 45px;
    line-height: 45px;
    width: 45px;
    font-size: 1rem;
  }
  .ico{
    font-size: 1.4rem;
    color: #ccc;
  }
  .titleButton:active{
    background-color: aqua;
  }
  svg{
    height: 24px;
    width: 24px;
    fill: #ccc;
  }
  .loading{
    margin: 6px;
  }
}
</style>