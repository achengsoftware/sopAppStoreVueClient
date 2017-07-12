<template lang="pug">
.gender-setting
    TitleBar(title='性别',leftIcon="flase")
    .option-box(@click="checkGender(0)")
      .text 未设置
      .is-check.ico(v-show="genderID == 0") &#xe609;
    .option-box(@click="checkGender(1)")
      .text 男
      .is-check.ico(v-show="genderID == 1") &#xe609;
    .option-box(@click="checkGender(2)")
      .text 女
      .is-check.ico(v-show="genderID == 2") &#xe609;
</template>

<script>
import TitleBar from '../brick/Title'
import { DATA } from "../method.js" 
export default {
  components: {
    TitleBar,
  },
  data(){
    return {
      genderID: null
    }
  },
  mounted(){
    //this.genderID = this.$route.params.id
  },
  methods: {
    checkGender (genderID){
      //this.genderID = genderID
      //CHANNEL.updateAccount( JSON.stringify({type:3, gander: genderID}) )
      //history.go(-1);
    }
  }
}
</script>

<style lang='less' scoped>
.gender-setting{
  .option-box{
    background-color: white;
    display: flex;
    justify-content: space-between;
    padding: 0 15px;
    height: 40px;
    line-height: 40px;
    border-bottom: 1px solid #eeedee;
  }
  .is-check{
    color: chartreuse;
  }
}
</style>