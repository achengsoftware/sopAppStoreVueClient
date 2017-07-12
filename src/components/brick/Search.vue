<template>
  <div class="search-box">
    <div class="text-bar">
      <input v-model="searchText" class="text-input" type="text" name="fname" placeholder="搜索"/>
      <img v-show="searchText" @click="clear" class="forgo" src="data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAABkAAAAZCAYAAADE6YVjAAACxUlEQVR42q1WS2sTURQe0JWp4kIl6KIWi1XcCNXWPqh1IbiW4s6FUBT8E278E4IxkIQuZpFJJp0kk8wzO6tWEf0BKqVS7dtmMq1GvzNO6uU282jrwKW5955zvvP47jkVhIBPVdWEoqi3dV3PW5a1aNv2dqPRaNPC7x3LsldwPlev158oitIriuIRYT+fLMvHYPw+DJHh31ELoD+r1eoD0osFMDMjnzJN81Mc4/xCZF/y+fyZUACEfRWCSwcB+Adkf5ckabArAHJ6GmF/OwwAE9Ey7CX31MAwjM+c4FuAujFr4iLF8+wZ9guZjJrYZREVjROYpzsw5zIMrEd4vSHL1Sskb1mN1+xdpVJ55IFomnaH2MEpOtms2M8AuQERbHcAwMZe6G1x9+1cLncCIHqpmwHTtNay2Wy/b2AUCi0+RbVabYzu4fF57Fe62dE0c0qAsaWQVDilUmnIj2gSe7cTAVJ8i85nZ9VB7JtBNuDgc8G2GzsROW/C03EmIgeA4349R3H/I0wfOh8Ev1WEsgdEcPCGhllGIlXX+BoE6G7FAqEFijdZkKgI2CXwzAp6B5QqroHuIUOgc/BoLQKghZpMkmEUewjhb4IMI37KbkLGiQD5SJG8CSm6Sx7/NWiMdVhErEONJugcRobDIsL9C1BTfxoQwToALgUVGdR3isXiDf+dDEC+a0bAxHuU2z4I/OJThH7mAZTL5QtE24BIW+gMA34Hv8injl58Op0+icdoHkVrmeZo94oUJckDWI14R9QZPCDovWTvEMDjXaZkMpmEadoLjEAbCnP424zZ2ls+QJtx9CtWDz9PkjRw/tM8WQUDzwXN9uso6PJhAKhRgggjoSNYFJUkDZyDAEBvsVAonI31zwQNMtB2mmddiPdtVa0/RMp7hP1+qVTqOB7TXbDvGbx8Zxjmhu/xJtrMezqHQ1PeYAr5/gAaUh813BsrogAAAABJRU5ErkJggg==" />
    </div>
  </div>
</template>

<script>
import { Order } from '../Order.js'
export default {
  data () {
    return {
      searchText:""
    }
  },
  methods: {
    clear () {
      this.searchText = ''
    }
  },
  watch: {
    searchText(val) {
      Order.$emit('Search', val)
    }
  }
}
</script>

<style lang="less" scoped>
.search-box{
    height: 30px;
    background-color: #f8f8f8;
    border-bottom: 1px solid #dfdde8;
    padding: 5px 10px;
    .text-bar{
        background-color: #dbdbdb;
        height: 30px;
        width: 100%;
        margin: 0 auto;
        border-radius: 5px;
        position: relative;
    }
    .text-input{
        width: 100%;
        height: 100%;
        margin: 0 auto;
        border: none;
        padding: 0;
        background-color: #dbdbdb;
        border-radius: 5px;
        text-align: center;
        font-size: 0.9rem;
    }
    .forgo{
        position: absolute;
        right: 5px;
        top: 5px;
        height: 20px;
    }
}
</style>