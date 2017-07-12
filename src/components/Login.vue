<template lang="pug">
.login-box(:class = "{ isSelectd: selectList }")
  .logo
    img(src="../assets/logo.png")
    p 智慧企业运行平台
  .login(:class="{ hide: selectList }")
    .input-box
      .user-name
        .ico &#xe60c;
        input(v-model="userName",placeholder="用户名")
      .password
        .ico &#xe623;
        input(type="password",v-model="password",placeholder="密码")
  .button(@click="preLogin()",:class="{ hide: selectList }") 登录
  .step
    .ico.one &#xe602;
    .ico.two &#xe602;
  .select-list(v-show="selectList")
    .title
      span.ok 共查找到 {{orgNumber}} 个组织
      .close.ico(@click="selectList = null") &#xe697;
    ul.list(ref="iscroll",:options="{preventDefault: false}")
      li(v-for="item in selectList",v-on:click="login(item)") {{item.unitName}}
  Loading(text="正在登录")
  Toast
</template>

<script>
import Loading from './brick/Loading'
import Toast from './brick/Toast'
import { Order } from './Order.js'
import { DATA, log, CHANNEL } from "./method.js"
import localforage from 'localforage'

export default {
  data () {
    return {
      userName: '',
      password: '',
      selectList: null,
      orgNumber: 0
    }
  },
  components: {
    Toast,
    Loading
  },
  mounted(){
    if(this.$route.params.name) this.userName = this.$route.params.name // 判断是否有保存的用户名
  },
  methods: {
    preLogin: function() { //预登录函数
      //判断是否为debug模式
      if(DATA.debug) { window.location.href="#/Main"; return; }
      //判断用户名和密码是否为空
      if( this.userName === '' || this.password === '' ){ Order.$emit('Toast', '请正确输入账号和密码'); return null; }
      //预登录信号监听
      Order.$once('preLogin', (message) => {
        if(!message) {Order.$emit('Toast', '返回数据为空！'); return;}
        Order.$emit('Loading', 'hide')
        setTimeout( ()=>{
          switch(message.length){ //判断同名用户数量
            case 0  : Order.$emit('Toast', '登录超时'); break; 
            //如果用户所属的组织只有一个，那么自动帮用户选择登录
            case 1  : const data = message[0]; this.login(data); break;
            default : this.selectList = message; this.orgNumber = message.length;
          }
        },0);
      })
      Order.$emit('Loading', 'show')
      CHANNEL.preLogin( `{"userName":"${this.userName}","password":"${this.password}"}` )
    },
    login: function(thisOrg){ //登录函数
      DATA.org = thisOrg //存储组织信息
      Order.$emit('Loading', 'show')
      //登录信号监听
      Order.$once('login', (message)=> {
        //登录验证成功后执行的方法
        function loginSuccess(){
          const nowTime = new Date().getTime()
          //从本地文件中读取缓存
          CHANNEL.readData((data)=>{
            const cache = JSON.parse(data)
            if(cache.org.usbkeyidentification === DATA.org.usbkeyidentification){
              DATA.orgTree = cache.orgTree
              DATA.orgList = cache.orgList
              DATA.appList = cache.appList
              DATA.installedAppID = cache.installedAppID
            }
          })
          const appData = {
            Timestamp: nowTime, //时间戳
            org: DATA.org
          }
          DATA.Timestamp = nowTime
          //保存用户信息
          localforage.setItem('appData', appData, function (err){
            if(err){ Order.$emit('Toast', '缓存用户数据失败'); return null; } //错误处理
            const data = JSON.stringify({type:"8",appType: "1",appID: "0", orgID: DATA.org.orgID, unitID: DATA.org.unitId, orgCode: DATA.org.orgCode})
            CHANNEL.queryAppStore(data)
            window.location.href="#/Main"
          });
        }
        setTimeout(()=>{
          Order.$emit('Loading', 'hide')
          //判断错误码是否为 0:成功 113:已登录
          switch(message.code){
            case 0  :  loginSuccess(); break;
            case 113:  loginSuccess(); break;
            case 112:  Order.$emit('Toast', `用户名或密码错误`); break;
            default :  Order.$emit('Toast', `登录失败 Code:${message.code}`)
          }
          this.selectList = null
        },0)
      })
      CHANNEL.login(JSON.stringify({
        usbkeyidentification : DATA.org.usbkeyidentification, //身份证
        password : this.password, //密码
        unitId : DATA.org.unitId, //所在组织id
        userName: DATA.org.enname, //用户名
      }))
    },
  },
}
</script>

<style lang='less' scoped>
.logo {
  width: 180px;
  margin: 0 auto;
  padding: 20px 0;
  height: 180px;
  display: block;
  img {
    height: 120px;
    width: 180px;
    margin: 0;
  }
  p {
    color: #A9A9A9;
    font-size: 19px;
    text-align: center;
  }
}
.input-box{
  width: 300px;
  margin: 0 auto;
  margin-top: 30px;
  border-radius: 5px;
  overflow: hidden;
  border: 1px solid #e3e3e3;
}
.login{
  height: 130px;
  .user-name{
    border-bottom: 1px solid #fefefe;
  }
  .password{
    border-top: 1px solid #e3e3e3;
  }
  .user-name,.password{
    height: 50px;
    width: 100%;
    display: flex;
    background-color: white;
    input{
      height: 50px;
      border: 0;
      background-color: white;
      width: 245px;
      font-size: 1rem;
      padding: 0;
    }
    .ico{
      height: 50px;
      width: 50px;
      font-size: 1.2rem;
      text-align: center;
      line-height: 50px;
      color: #a4a9b2;
    }
  }

}
.button{
  width: 300px;
  height: 45px;
  border-radius: 5px;
  margin: 40px auto;
  background-color: #333366;
  text-align: center;
  line-height: 45px;
  color: white;
  font-size: 20px;
  box-shadow: 1px 1px 1px #888888;
}
.button:active{
  background-color: blue;
}
.select-list{
    height: 280px;
    margin: 0 auto;
    position: absolute;
    top: 210px;
    left: 0;
    right: 0;
    width: 90%;
    border-radius: 5px;
    .title{
        height: 40px;
        background-color: #dcd6d6;
        border-radius: 5px 5px 0 0;
        position:relative;
        .ico{
            color: cadetblue;
            position: absolute;
            right: 7px;
            top: 7px;
        }
    }
    .ok{
        color: cadetblue;
        line-height: 40px;
        margin-left: 10px;
    }
    .list{
        touch-action: none;
        overflow: auto;
        height: 220px;
        border: 1px solid #dcecec;
        border-radius: 0 0 5px 5px;
        li{
            padding: 10px;
            border-bottom: 1px solid #dcecec;
            text-align: center;
        }
        li:active{
            background-color: #4899E0;
            color: #FFF;
        }
    }
}

.step{
  width: 100%;
  text-align: center;
  color: #ccc;
  height: 30px;
  .one {
    color: blue;
  }
}
.ico{
  font-size: 1.4rem;
  color: #ccc;
}
.hide{
  visibility:hidden;
}
.isSelectd {
  width: 100%;
  overflow: hidden;
  height: calc(~"100% - 45px");
  .one {
    color: #ccc;
  }
  .two {
    color: blue;
  }
}
@media screen and (max-height: 200px) {
  .logo{
    display: none;
  }
}
</style>