<template>
  <div class="toast-box" v-show="text">
    <span class="text">{{text}}</span>
  </div>
</template>

<script>
import { Order } from '../Order.js'
export default {
  data () {
    return {
      text: null
    }
  },
  mounted () {
    const _this = this
    Order.$on('Toast', function(message) {
      _this.text = message
      function hide(){
        _this.text = null
      }
      setTimeout(hide,3000)
    })
  },
}
</script>

<style>
.toast-box{
    position: fixed;
    bottom: 60px;
    width: 100%;
    display: flex;
    justify-content: center;
    z-index: 999;
}
.toast-box span{
    height: 30px;
    background-color: rgba(0, 0, 0, 0.5);
    padding: 2px 10px;
    font-size: 1rem;
    line-height: 30px;
    border-radius: 5px;
    color: white;
}
</style>