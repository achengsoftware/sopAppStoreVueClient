<template lang="pug">
.app-store-box
  TitleBar(title='应用商店',leftIcon="ok",:rightIcon="leftIcon")
  Search
  .content
    .checker
      .checker-item(v-on:click="select='all'",:class="{ 'item-selected': select=='all' }") 全部
      .checker-item(v-for="item in selectItem",:class="{ 'item-selected': click(item.classifyID) }",v-on:click="select=item.classifyID",:key="item.classifyID") {{item.classifyName}}
    vroll.store-list(ref="iscroll",:options="{preventDefault: false}")
      li.app-list(v-for="item in classification",:key="item.id")
        img(:src="item.icon")
        .info
          p.name {{item.name}}
          p.detail 版本号:{{item.version}}
        .button.open(v-if="item.installed === 1",v-on:click="lookInfo(item,$event)") 已安装
        .button.down(v-if="item.installed === 0",v-on:click="installApp(item,$event)") 安装
        .button.down(v-if="item.installed === 2",v-on:click="needDown(item,$event)") 需下载
        .button.down(v-if="item.installed === 3",v-on:click="needDown(item,$event)") 更新
  .no-item(v-if="showNoItem") 
    p.ico &#xe62a;
    p.text 没有应用
  Toast
</template>

<script>
import Search from '../brick/Search'
import TitleBar from '../brick/Title'
import localforage from 'localforage'
import { Order } from '../Order.js'
import Toast from '../brick/Toast'
import { timeoutDetection, log, DATA, CHANNEL } from "../method.js"
const $TGYY = require('../../assets/TGYY.png')
export default {
  components: {
    Search,
    TitleBar,
    Toast
  },
  data () {
    return {
      select: 'all',
      selectItem: null,
      appStoreList: null,
      appData: null,
      text: "",
      installedAppID: null,
      leftIcon: "loading",
      downloading: false,
      showNoItem: false,
      appList: {}
    }
  },
  created () {
    //判断是否为debug模式
    if (DATA.debug) {
      this.appStoreList = [
        { classify: "all", url: "", icon: $TGYY, id: "0", status: "0", version: "0.1.0", name: "测试应用", type: "all" }
      ]
      return
    }
    this.installedAppID = DATA.installedAppID
    //监听应用被删除事件
    Order.$on('delateApp', (message) => {
      this.installedAppID = message
      log(this.installedAppID)
    })
    if (timeoutDetection()) { return null } //时间处理
    if (DATA.selectItem && DATA.appInfoList) { //缓存判断
      this.selectItem = DATA.selectItem
      this.appStoreList = DATA.appInfoList
    }
    else {
      //----------------------------分类条处理----------------------------
      Order.$once('classifyBeans', (message) => {
        DATA.selectItem = message.classifyBeans
        DATA.selectItem.forEach((element) => {
          this.appList[element.classifyID] = element.classifyName
        }, this)
        setTimeout(() => {
          this.selectItem = message.classifyBeans
        }, 0);
      })
      CHANNEL.queryAppStore(JSON.stringify({ type: "4" }))
      //----------------------------应用列表处理----------------------------
      Order.$once('appStores', (message) => {
        let appInfoList = message.appStore.appInfoList
        if (appInfoList.length === 0) {
          setTimeout(() => {
            this.leftIcon = "no"
            this.showNoItem = true
          })
        }
        else {
          //存储应用列表信息
          DATA.appInfoList = appInfoList
          setTimeout(() => {
            this.appStoreList = appInfoList
            this.leftIcon = "no"
            const iscroll = this.$refs.iscroll
            iscroll.refresh()
          }, 0);
        }
      })
      CHANNEL.queryAppStore(JSON.stringify({ type: "2" }))
    }
    Order.$on('Search', (message) => {
      // 在这里打log看是否能监听到
      this.text = message
    })
  },
  methods: {
    openStart: function (item) { //判断以何种方式打开应用
      window.location.href = item.homeUrl;
    },
    openApp: function () { //打开应用
      const app1 = {
        "type": 2,
        "sopid": "com.vrv.linkDood",
        "pkgpath": "com.vrv.linkDood-1.0.45.sop",
        "scheme": "linkdood:showlinkdood?id=" + DATA.org.usbkeyidentification,
        "name": "linkdood"
      };
      //打开应用
      CHANNEL.opensopApp(JSON.stringify(app1))
    },
    installApp: function (item, element) { // 安装应用
      if (item.type === 2) { //判断是否是H5应用
        CHANNEL.queryAppStore(JSON.stringify({ type: "6", id: item.id, classify: item.classify }))
          if (!DATA.installedAppID[item.id]) {
          DATA.installedAppID.push(item.id)
        }
        //清除它的选中状态
        item.isSelect = false
        if (DATA.appList[this.appList[item.classify]]) {
          let same = null;
          for (let x in DATA.appList[this.appList[item.classify]]) {
            if (DATA.appList[this.appList[item.classify]][x].id === item.id) {
              same = x;
            }
          }
          if (!DATA.appList[this.appList[item.classify]][same]) {
            DATA.appList[this.appList[item.classify]].push(item)
          }
        }
        else {
          DATA.appList[this.appList[item.classify]] = [item]
        }
      }
      else {
        if (this.downloading) { Order.$emit('Toast', '正在下载请稍后'); return; }
        // 判读本地应用列表的版本是否高于网络获取的版本
        let sopid = null
        if (item.packageName.indexOf('.sop') > -1) {
          sopid = item.packageName.split('-')[0]
        }
        if (DATA.systemAppList[sopid]) {
          if (DATA.systemAppList[sopid].ver >= item.version.replace('V', '')) {
            element.target.innerHTML = '已安装'
            element.target.setAttribute("class", "button open");
              if (!DATA.installedAppID[item.id]) {
          DATA.installedAppID.push(item.id)
        }
        //清除它的选中状态
        item.isSelect = false
        if (DATA.appList[this.appList[item.classify]]) {
          let same = null;
          for (let x in DATA.appList[this.appList[item.classify]]) {
            if (DATA.appList[this.appList[item.classify]][x].id === item.id) {
              same = x;
            }
          }
          if (!DATA.appList[this.appList[item.classify]][same]) {
            DATA.appList[this.appList[item.classify]].push(item)
          }
        }
        else {
          DATA.appList[this.appList[item.classify]] = [item]
        }
          }else{
               this.downloading = true
     //     item.homeUrl = item.activityName
          Order.$on('progress', (message) => {
            element.target.innerHTML = `${message.progress}%`
          })
          Order.$once('downloadApp', (message) => {
            Order.$off("progress")
             let appName = item.packageName.split('-')[0]
             DATA.systemAppList[appName] = { ver: item.version.replace('V', '') }
            setTimeout(() => {
              this.downloading = false
              element.target.innerHTML = '已安装'
              item.installed = 1
              element.target.setAttribute("class", "button open");
              CHANNEL.queryAppStore(JSON.stringify({ type: "6", id: item.id, classify: item.classify }))
              if (!DATA.installedAppID[item.id]) {
                CHANNEL.installSopApp(item.packageName)
              }
              if (!DATA.installedAppID[item.id]) {
                DATA.installedAppID.push(item.id)
              }
            //清除它的选中状态
            item.isSelect = false
            if (DATA.appList[this.appList[item.classify]]) {
              let same = null;
              for (let x in DATA.appList[this.appList[item.classify]]) {
                if (DATA.appList[this.appList[item.classify]][x].id === item.id) {
                  same = x;
                }
              }
              if (!DATA.appList[this.appList[item.classify]][same]) {
                DATA.appList[this.appList[item.classify]].push(item)
              }
            }
            else {
              DATA.appList[this.appList[item.classify]] = [item]
            }
                }, 0)
              })
              CHANNEL.downloadApp(item.packageName, item.downloadUrl)
              }
            } else {
              this.downloading = true
              item.homeUrl = item.activityName
              Order.$on('progress', (message) => {
                element.target.innerHTML = `${message.progress}%`
              })
              Order.$once('downloadApp', (message) => {
                Order.$off("progress")
                let appName = item.packageName.split('-')[0]
                DATA.systemAppList[appName] = { ver: item.version.replace('V', '') }
                setTimeout(() => {
                  this.downloading = false
                  element.target.innerHTML = '已安装'
                  item.installed = 1
                  element.target.setAttribute("class", "button open");
                  CHANNEL.queryAppStore(JSON.stringify({ type: "6", id: item.id, classify: item.classify }))
                  if (!DATA.installedAppID[item.id]) {
                    CHANNEL.installSopApp(item.packageName)
                  }
                  //---------------------------------------------------
                    if (!DATA.installedAppID[item.id]) {
                       DATA.installedAppID.push(item.id)
                    }
                //清除它的选中状态
                item.isSelect = false
                if (DATA.appList[this.appList[item.classify]]) {
                  let same = null;
                  for (let x in DATA.appList[this.appList[item.classify]]) {
                    if (DATA.appList[this.appList[item.classify]][x].id === item.id) {
                      same = x;
                    }
                  }
                  if (!DATA.appList[this.appList[item.classify]][same]) {
                    DATA.appList[this.appList[item.classify]].push(item)
                  }
                }
                else {
                  DATA.appList[this.appList[item.classify]] = [item]
                }
                    }, 0)
                  })
                  CHANNEL.downloadApp(item.packageName, item.downloadUrl)
                }
            
              }

        //  if (!DATA.installedAppID[item.id]) {
        //   DATA.installedAppID.push(item.id)
        // }
        // //清除它的选中状态
        // item.isSelect = false
        // if (DATA.appList[this.appList[item.classify]]) {
        //   let same = null;
        //   for (let x in DATA.appList[this.appList[item.classify]]) {
        //     if (DATA.appList[this.appList[item.classify]][x].id === item.id) {
        //       same = x;
        //     }
        //   }
        //   if (!DATA.appList[this.appList[item.classify]][same]) {
        //     DATA.appList[this.appList[item.classify]].push(item)
        //   }
        // }
        // else {
        //   DATA.appList[this.appList[item.classify]] = [item]
        // }
      // localforage.getItem("appData", (err, appData) => {
      //   appData.appList = DATA.appList
      //   appData.installedAppID = DATA.installedAppID
      //   localforage.setItem('appData', appData)
      // })
    },
    needDown: function (item, element) {
      log('gengxin')
      if (item.installed > 1) {
        // 减少
        DATA.updateNumber--
        let appInformation = {
          id: item.id,
          type: item.type,
          name: item.name,
          icon: item.icon,
          packageName: item.packageName,
          status: 1
        }
        if (this.downloading) { Order.$emit('Toast', '正在下载请稍后'); return; }
        this.downloading = true
        item.homeUrl = item.activityName
        Order.$on('progress', (message) => {
          element.target.innerHTML = `${message.progress}%`
        })
        Order.$once('downloadApp', (message) => {
          Order.$off("progress")
          setTimeout(() => {
            this.downloading = false
            // 将应用ID添加到已安装应用列表
            element.target.innerHTML = '已安装'
            item.installed = 1
            DATA.systemAppList[item.packageName.split('-')[0]].ver = item.version.replace('V', '')
            element.target.setAttribute("class", "button open");
            DATA.installedAppID.push(item.id)
            CHANNEL.installSopApp(item.packageName)
          }, 0)
        })
        CHANNEL.downloadApp(item.packageName, item.downloadUrl)
        //清除它的选中状态
        item.isSelect = false
        item.needUpdata = false
        DATA.appList.forEach((thisApp, index) => {
          if (item.packageName === thisApp.packageName) {
            DATA.appList[index] = item
          }
        })
        // localforage.getItem("appData", (err, appData) => {
        //   appData.appList = DATA.appList
        //   appData.installedAppID = DATA.installedAppID
        //   localforage.setItem('appData', appData)
        // })
      }
    },
    click: function (key) {
      return this.select == key
    }
  },
  mounted () {
    this.installedAppID = DATA.installedAppID
  },
  computed: {
    //筛选应用
    classification: function () {
      const newList = {}
      for (let item in this.appStoreList) {
        const data = this.appStoreList[item]
        //判断应用列表的类型是否和选择的类型一致
        if (this.select === "all" || data.classify == this.select) {
          // 判断应用是否标记为可用
          if (data.status === 1) {
            // 判断是否有搜索内容
            if (this.text == "" || data.name.indexOf(this.text) > -1) {
              if (this.installedAppID.indexOf(data.id) < 0) data.installed = 0
              else {
                // 判断应用是否为原生应用
                if (data.type === 1) {
                  // 判断本地应用列表是否有该应用
                  let dataVersion = data.packageName.split('-')[0]
                  // log(DATA.systemAppList)
                  if (DATA.systemAppList[dataVersion]) {
                    // 判断本地已安装版本号 是否和 网络最新版本一致
                    let netVersion = data.version.replace('V', '')
                    let systemAppListVersion = DATA.systemAppList[dataVersion].ver
                    if (systemAppListVersion < netVersion) {
                      data.installed = 3
                    } else {
                      data.installed = 1
                    }
                  }
                  else {
                    data.installed = 0
                  }
                }
                else {
                  data.installed = 1
                }
              }
              newList[item] = data
            }
          }
        }
      }
      return newList
    }
  }
}
</script>

<style lang='less' scoped>
.app-store-box {
  overflow: hidden;
  height: 100%;
  .checker {
    height: 24px;
    line-height: 24px;
    padding: 8px;
    display: flex;
    background-color: #f4f4f4;
    border-bottom: 1px solid #e5e5e5;
    font-weight: 400;
  }
  .checker-item {
    height: 24px;
    line-height: 24px;
    text-align: center;
    color: blue;
    font-size: 0.7rem;
    margin: 0 5px;
    padding: 0 5px;
  }
  .item-selected {
    background-color: #1865ff;
    border-radius: 20px;
    color: white;
  }
}

.app-list {
  display: flex;
  background-color: white;
  img {
    width: 45px;
    height: 45px;
    margin: 7px;
  }
  .info {
    width: calc(~"100% - 150px");
  }
  p {
    height: 30px;
  }
  .name {
    font-size: 0.9rem;
    line-height: 33px;
  }
  .detail {
    font-size: 0.8rem;
    line-height: 30px;
    color: #7b7b7b;
  }
  .button {
    width: 60px;
    height: 30px;
    border-radius: 5px;
    margin: 16px 13px;
    text-align: center;
    line-height: 30px;
    font-size: 0.9rem;
  }
  .button:active {
    background: cornflowerblue;
    color: white;
  }
  .open {
    border: 1px solid black;
  }
  .down {
    color: white;
    background-color: #1865ff;
  }
}

.store-list {
  touch-action: none;
  overflow: hidden;
  height: 443px;
}

.no-item {
  position: fixed;
  top: 250px;
  left: 140px;
  color: #ccc;
  .ico {
    font-size: 5rem;
  }
  p {
    text-align: center;
  }
}
</style>