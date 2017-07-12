<template lang="pug">
.notice-box
  TitleBar(title='通知')
  ul.notice-list
    Refresh(v-if="thread !== 0")
    li(v-for='item in noticeList',v-if="item",@click="openURL(item)")
      img(:src='item.img')
      .message
        p {{item.name}}
        p.text {{item.text}}
      .time {{item.time}}
      .notice(v-if='item.notice != "0"') {{item.notice}}
  Toast
  BottomBar(index="0")
</template>

<script>
import Refresh from './brick/Refresh'
import Toast from './brick/Toast'
import TitleBar from './brick/Title'
import BottomBar from './brick/Bottom'
import { Order } from './Order.js'
import { get, cutString, timeoutDetection, DATA, STATE, log, CHANNEL, dataDetection } from "./method.js" 
// 引入图片资源
const $XXFB    = require('../assets/XTBG.png'),
      $AQYJ    = require('../assets/YJ.png'),
      $GWGL    = require('../assets/GWGL.png');

export default {
  components: {
    TitleBar,
    BottomBar,
    Toast,
    Refresh
  },
  data () {
    return {
      noticeList: {
        "XXFB": null,
        "GWGL": null,
        "AQYJ": null
      },
      thread: 0
    }
  },
  mounted(){
    //判断是否为debug模式
    if(DATA.debug){ 
      const noticeData = { 
        img    : $XXFB,
        name   : '协同办公',
        text   : '这里是邮件的题目',
        time   : '2066年6月6日',
        notice : '99+',
        url    : 'owo.help'
      }
      this.thread++
      this.$set(this.noticeList, "XXFB", noticeData)
      return
    }

    // const data = {
    //   userID: DATA.org.usbkeyidentification + "",
    //   appID: 7616946181 + "",
    //   accountType: 2 + ""
    // }
    // Order.$once('getLoginAuthCode', (message)=> {
    //   log(message)
    // })
    // CHANNEL.getLoginAuthCode(JSON.stringify(data))

    //---------------------检测------------------------
    if(timeoutDetection()) { return null } //超时检测
    dataDetection() //数据被清除检测
    //------------------------------------------------
    // 监听客户端发来的程序被激活事件
    Order.$on('refreshData', (message)=> {
      this.getMail() // 获取邮件
      if(DATA.org.unitId == "1") { this.getBacklog() } // 集团用户检测
      else{ this.getBumph() }
    })
  },
  activated(){
    if(DATA.debug) return
    this.getMail()
    if(DATA.org.unitId == "1") { this.getBacklog() } // 集团用户检测
    else { this.getBumph() }
  },
  methods:{
    openURL: function(item) {
      //统计接口 这里的statistics为手拼应用识别码
      CHANNEL.queryAppStore(item.statistics)
      if(item.url == "#"){
        const app =  {
          "scheme": `casicoa:showOA?pid=${DATA.org.usbkeyidentification}&sessionID=54545333`
        }
        CHANNEL.opensopApp(JSON.stringify(app))
      }
      else{
        const url = item.url.replace("http","browser")
        const app1 = {
          "scheme":url,
        };
        CHANNEL.opensopApp(JSON.stringify(app1))
      }
    },
    getDateDiff: function(nS) {
      let result;
      if(nS < 10000000000) nS = nS * 1000
      const diffValue = new Date().getTime() - nS;
      if (diffValue < 0) {
        return "时间戳错误";
      }
      const monthC = parseInt(diffValue / 2592000000);
      const weekC = parseInt(diffValue / (7 * 86400000));
      const dayC = parseInt(diffValue / 86400000);
      const hourC = parseInt(diffValue / 3600000);
      const minC = parseInt(diffValue / 60000);
      if (monthC >= 1) {
        result = monthC + "月前";
      } else if (weekC >= 1) {
        result = weekC + "周前";
      } else if (dayC >= 1) {
        result = dayC + "天前";
      } else if (hourC >= 1) {
        result = hourC + "小时前";
      } else if (minC >= 1) {
        result = minC + "分钟前";
      } else {
        result = "刚刚";
      }
      return result;
    },
    getMail: function() {
      //--------------------------------------------邮件通知获取--------------------------------------------------------
      if(STATE.getMail) return;
      STATE.getMail = true
      const YJURL = `http://10.152.36.31/secmail/GetAppUnreadFileService?id_card=${DATA.org.usbkeyidentification}&username=secmail&password=welcome`
      this.thread++
      get( YJURL, (receive)=> {
        if(receive ==="" || receive === null ) { Order.$emit('Toast', '获取通知数据失败'); return null } //空数据检测
        if(receive.length === 19) {STATE.getMail = false;this.thread--;return;}
        const data = JSON.parse(receive)
        const AQYJ = { // 安全邮件
          img    : $AQYJ,
          name   : '安全邮件',
          text   : '暂无待处理邮件',
          time   : '',
          notice : '0',
          url    : `http://10.152.36.20/secmail/loginapp.do?type=cid&PID=${DATA.org.usbkeyidentification}&type2=Unread`,
          statistics: `{"type":"8","appType":"2","appID":"100004","orgID":"${DATA.org.orgID}","unitID":"${DATA.org.unitId}","orgCode":"${DATA.org.orgCode}"}`
        }
        if(data.length > 0){
          const date = Date.parse(data[0].send_date.replace(/-/gi,"/"))
          AQYJ.text = data[0].subject
          AQYJ.time = this.getDateDiff(date)
          let number = parseInt(data[0].count)
          if(number > 99) number = '99+'
          AQYJ.notice = number
        }
        // 将 *应用数据* 显示在界面上
        setTimeout(()=> {
          STATE.getMail = false
          this.$set(this.noticeList,"AQYJ",AQYJ)
          this.thread--
        },0)
      })
    },
    getBacklog: function() {
      //--------------------------------------------协同通知获取--------------------------------------------------------
      if(STATE.getBacklog) return;
      STATE.getBacklog = true
      const XXFBURL = 'http://10.152.36.26:8080/CASIC/interfaces/304DaiBanInterface.jsp?userName='+ DATA.org.enname +'&PID='+DATA.org.usbkeyidentification+'&webService='
      this.thread++
      //通过Get请求请求通知数据
      get( XXFBURL, (receive)=> {
        if(receive ==="" || receive === null ) { Order.$emit('Toast', '获取通知数据失败'); return null } //空数据检测
        let number = parseInt(cutString(receive,"wdNum>","<"))
        if(number > 99) number = '99+'
        const date = Date.parse(cutString(receive,"SentTime>","<").replace(/-/gi,"/"))
        //给 *应用数据* 的备份 增加 *通知数据*
        const XXFB = { // 信息发布
          img    : $XXFB,
          name   : '协同办公',
          text   : cutString(receive,"Title>","<"),
          time   : this.getDateDiff(date),
          notice : number,
          url    : 'http://10.152.36.26:8080/page_m/dblist.jsp?userName=' + DATA.org.enname + '&PID='+ DATA.org.usbkeyidentification + '&webService=',
          statistics: `{"type":"8","appType":"2","appID":"100000","orgID":"${DATA.org.orgID}","unitID":"${DATA.org.unitId}","orgCode":"${DATA.org.orgCode}"}`
        }
        // 将 *应用数据* 显示在界面上
        setTimeout(()=> {
          STATE.getMail = false
          this.$set(this.noticeList,"XXFB",XXFB)
          this.thread--
        },0)
      })
    },
    getBumph: function() {
      const GWGLURL = `http://10.152.36.33:8080/CasicOA/std/entity/page_data.tsp?objectName=WfActivity!portal&objectEvent=Query&$bizId=my_all_without_doc_mobile&isMobile=y&PID=${DATA.org.usbkeyidentification}`
      if(STATE.getBumph) return;
      STATE.getBumph = true
      this.thread++
      get( GWGLURL, (receive)=> {
        if(receive ==="" || receive === null ) { Order.$emit('Toast', '获取通知数据失败'); return null } //空数据检测
        if(receive.length === 34) {STATE.getBumph = false;this.thread--;return;}
        const data = JSON.parse(receive)
        const GWGL = { // 公文管理
          img    : $GWGL,
          name   : '公文管理',
          text   : '暂无待处理公文',
          time   : '',
          notice : "0",
          url    : '#',
          statistics: `{"type":"8","appType":"2","appID":"100001","orgID":"${DATA.org.orgID}","unitID":"${DATA.org.unitId}","orgCode":"${DATA.org.orgCode}"}`
        }
        if(data.pageData.length > 0){
          const date = Date.parse(data.pageData[0].startTime.replace(/-/gi,"/"))
          GWGL.text = data.pageData[0]["wfInstance.description"]
          let number = data.pageData.length
          if(number > 99) number = '99+'
          GWGL.time = this.getDateDiff(date)
          GWGL.notice = number
        }
        setTimeout(()=> {
          STATE.getBumph = false
          this.$set(this.noticeList,"GWGL",GWGL)
          this.thread--
        },0)
      })
    }
  }
}
</script>

<style lang='less' scoped>
.notice-list{
  height: calc(~"100% - 95px");
  overflow-y: hidden;
  li{
    display: flex;
    height: 65px;
    border-bottom: 1px solid #eaeaea;
    position: relative;
    background-color: white;
    overflow: hidden;
    width: 100%;
    img{
      width: 45px;
      height: 45px;
      margin: 10px;
      overflow: hidden;
    }
    .message{
      margin: 10px;
      width: calc(~'100% - 115px');
      overflow: hidden;
    }
    .text{
      color: #8c8c8c;
      font-size: 0.8rem;
      width: 240px;
      height: 30px;
      line-height: 30px;
      white-space: nowrap;
      overflow: hidden;
      text-overflow: ellipsis;
    }
    .time{
      color: #d3d3d3;
      font-size: 0.6rem;
      text-align: center;
      overflow: hidden;
      position: absolute;
      right: 10px;
      top: 10px;
    }
    .notice{
      width: 20px;
      height: 20px;
      position: absolute;
      background-color: red;
      top: 2px;
      left: 45px;
      border-radius: 50%;
      color: white;
      line-height: 20px;
      text-align: center;
      font-size: 10px;
      overflow: hidden;
      font-family: Tahoma;
    }
  }
  li:active{
    background-color: #4899E0;
    color: #FFF;
  }
}
</style>