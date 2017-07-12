<template lang="pug">
.details-box
  TitleBar(title='详细资料',leftIcon="flase")
  .pre-inf
    img(src="../../assets/user.png")
    .inf
        p.name.item {{$route.params.name}}
        p.department.item {{$route.params.org}}
  .phone
    .Pan42
      .item 职位
      .text {{$route.params.duty}}
    .Pan42
      .item 固定电话
      .text {{$route.params.telPhone}}
      .call-box
        .tel.ico(v-on:click="callTel") &#xe603;
      
    .Pan42
      .item 手机号码
      .text {{$route.params.enMobile}}
      .call-box
        .phone.ico(v-on:click="call") &#xe604;
        .chat.ico(v-on:click="yuanyuan") &#xe60a;
        .mess.ico(v-on:click="sendMes") &#xe619;
</template>

<script>
import TitleBar from '../brick/Title'
import { DATA, CHANNEL } from "../method.js" 
export default {
  components: {
    TitleBar
  },
  data () {
    return {
      number:""
    }
  },
  methods: {
    sendMes:function(){
      const data={scheme:"sms:"+ this.$route.params.enMobile +"?body="};
      //发短信
      CHANNEL.opensopApp(JSON.stringify(data))
    },
    call:function(){
      //打电话
      CHANNEL.callPhone(this.$route.params.enMobile)
    },
    callTel:function(){
      //打电话
      CHANNEL.callPhone(this.$route.params.telPhone)
    },
    yuanyuan:function(){
      const app1 = {
        "type":2,
        "sopid":"com.vrv.linkDood",
        "pkgpath":"com.vrv.linkDood-1.0.45.sop",
        "scheme":"linkdood:showlinkdood?id=" + DATA.org.usbkeyidentification + "&pwd=123456",
        "name":"linkdood"
      };
      //打开应用
      CHANNEL.opensopApp(JSON.stringify(app1))
    }
  },
}
</script>

<style lang='less' scoped>
.details,.phone{
  margin: 20px 0;
}

.Pan42{
  height: 42px;
  line-height: 42px;
  padding: 0 15px;
  background-color: white;
  display: flex;
  font-size: 0.9rem;
  border-bottom: 1px solid #eaeaea;
  .item{
    width: 65px;
  }
  .text{
    width: 120px;
    margin: 0 10px;
  }
  .ico{
    margin: 0 10px;
    font-size: 1.2rem;
    color: burlywood;
  }
  .ico:active{
    color: skyblue;
  }
}
.pre-inf{
    display: flex;
    height: 85px;
    background-color: white;
    border-bottom: 1px solid #eaeaea;
    padding: 0 15px;
    margin: 10px 0;
    img{
        width: 60px;
        height: 60px;
        margin: 13px 0;
        border-radius:5px;
    }
    .item{
        height: 23px;
        line-height: 23px;
        font-size: 0.8rem;
    }
    .inf{
        margin-left: 20px;
    }
    .name{
        height: 50px;
        line-height: 63px;
        font-size: 1.2rem;
    }
    .department{
        color: #b2b2b2;
    }
}
</style>
