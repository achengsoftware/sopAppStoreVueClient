<template lang="pug">
.search-box
  TitleBar(title='搜索',leftIcon="ok")
  .text-bar
    input.text-input(v-model="searchText",type="text",name="fname",placeholder="输入人名关键字")
    .button(v-on:click="search") 搜索
  .search-result(v-if="searchResult")
    .search-result-title
      span 共搜索到了{{searchNum}}条结果
      .close.ico(@click.stop="clearSearch") &#xe697;
    vroll.organizationSearch(:options="{click: true,scrollbars: true}")
      Organization(v-for="item in searchResult",:key="item.id",:name="item.enName",:text="item.orgName",:enMobile="item.enMobile",:duty="item.duty",:telPhone="item.telPhone")
  img.load(v-if="searching",:src='imgPath')
</template>

<script>
import TitleBar from './brick/Title'
import Organization from './list/Organization'
import { Order } from './Order.js'
import { timeoutDetection, DATA, log, CHANNEL } from "./method.js"
export default {
  components: {
    TitleBar,
    Organization
  },
  data () {
    return {
      searchText: "",
      searchResult: null,
      searching: false,
      searchNum: null,
      imgPath: require("../assets/ring.gif")
    }
  },
  beforeMount () {
    this.searchResult = null;
    //注册搜索
    Order.$on('searchEnOS', (message) => {
      //定时器
      setTimeout(() => {
        this.searching = false
        this.searchResult = message.entUsers
        this.searchNum = message.entUsers.length
        this.searchText = ""
      }, 0);
    })
  },
  methods: {
    search: function () {
      if (this.searchText) {
        this.searchResult = null;
        setTimeout(() => {
          this.searching = true
        }, 140)
        const enOS = { enterId: 602, orgId: DATA.org.unitId + "", type: 2, name: this.searchText }
        CHANNEL.queryEnOS(JSON.stringify(enOS));
      }
      else {
        this.searchResult = null
      }
    },
    clearSearch: function () {
      this.searchResult = null
    }
  },
}
</script>

<style lang="less" scoped>
.search-box {
  height: 100%;
  background-color: #f8f8f8;
  position: relative;
  .text-bar {
    height: 30px;
    width: 100%;
    position: relative;
    display: flex;
    border-bottom: 1px solid #dfdde8;
  }
  .text-input {
    width: calc(~"100% - 60px");
    margin: 2px 5px;
    border: none;
    padding-left: 5px;
    background-color: #dbdbdb;
    font-size: 0.9rem;
    border-radius: 5px;
  }
  .button {
    line-height: 30px;
    height: 100%;
    width: 40px;
    text-align: center;
    color: #a1a1a1;
    font-size: 14px;
  }
  .button:active {
    background-color: aqua;
  }
}

.search-result {
  background-color: rgba(248, 248, 248, 1);
  position: relative;
  z-index: 100;
  .search-result-title {
    height: 30px;
    line-height: 30px;
    color: #ccc;
    padding-left: 10px;
    position: relitive;
    .close {
      position: absolute;
      right: 5px;
      font-size: 1.2rem;
      color: cadetblue;
    }
  }
  .organizationSearch {
    height: 470px;
    touch-action: none;
    overflow: hidden;
    z-index: 999;
  }
}
</style>