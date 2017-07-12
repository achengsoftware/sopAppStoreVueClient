<template lang="pug">
.contacts-box
  TitleBar(title='通讯录',rightIcon='search')
  .organization-bar
    template(v-for="(item, key) in tree")
      span.organization-item(v-on:click="clickTree(item, key)") {{item.orgName }}
      span >
  vroll.organization(ref="iscroll",v-if="List",:options="{click: true,scrollbars: true}")
    li(v-for="item in List.depts",v-on:click="load(item,true)",:key="item.orgID")
      img(src="../assets/Organization.png")
      p.organization-name {{item.orgName}}
      p.organization-number.ico &#xe61b; {{item.subOrgNum}}
      p.organization-people.ico &#xe60c; {{item.subUserNum}}
    Organization(v-for="item in List.entUsers",:key="item.id",:name="item.enName",:text="item.orgName",:enMobile="item.enMobile",:duty="item.duty",:telPhone="item.telPhone")
  img.load(v-else,:src="imgPath")
  BottomBar(index="2")
</template>

<script>
import TitleBar from './brick/Title'
import BottomBar from './brick/Bottom'
import Organization from './list/Organization'
import { Order } from './Order.js'
import { timeoutDetection, DATA, log, CHANNEL } from "./method.js"
export default {
  components: {
    TitleBar,
    BottomBar,
    Organization
  },
  data () {
    return {
      List: null,
      tree: "",
      imgPath: require("../assets/ring.gif")
    }
  },
  mounted () {
    timeoutDetection() //超时检测
    if (DATA.orgTree && DATA.orgTree.length > 0) {
      this.List = DATA.orgList
      this.tree = DATA.orgTree
      return
    }
    let orgName = DATA.org.unitName
    if (DATA.org.unitId == 1) orgName = "集团公司总部"
    this.load({
      orgName: orgName,
      orgID: DATA.org.unitId,
      subOrgNum: 666,
      subUserNum: 666,
    }, true)
  },
  activated () {
    const iscroll = this.$refs.iscroll
    iscroll.refresh()
  },
  methods: {
    load: function (Data, addTree) { //拉取层级数据
      this.List = null //显示加载动画
      //预登录信号监听
      Order.$once('queryEnOS', (message) => {
        setTimeout(() => {
          DATA.orgList = message
          this.List = DATA.orgList //显示层级数据
          if (addTree) {
            DATA.orgTree.push(Data) //层级树增加一层
            this.tree = DATA.orgTree //显示层级树
          }
          if (message.entUsers.length > 0) {
            //人员排序
            message.entUsers.sort((a, b) => {
              return (a.orderNum < b.orderNum) ? -1 : 1
            })
          }
        }, 0);

      })
      //判断组织数是否为空
      if (Data.subOrgNum === 0) {
        //服务器说 组织 和 人员数 都为空那就请求组织吧
        if (Data.subUserNum === 0) {
          //请求组织信息
          const enOS = { enterId: 602, orgId: Data.orgID + "", type: 4 }
          CHANNEL.queryEnOS(JSON.stringify(enOS));
          return
        }
        //请求人员信息
        const enOS = { enterId: 602, orgId: Data.orgID + "", type: 3 }
        CHANNEL.queryEnOS(JSON.stringify(enOS));
      }
      else {
        //请求组织信息
        const enOS = { enterId: 602, orgId: Data.orgID + "", type: 4 }
        CHANNEL.queryEnOS(JSON.stringify(enOS));
      }
    },
    clickTree: function (item, key) {
      //保存截取的层级树
      DATA.orgTree = DATA.orgTree.slice(0, key + 1)
      //显示层级树
      this.tree = DATA.orgTree
      this.load(item, false)
    },
  },
}
</script>

<style lang='less' scoped>
.organization {
  touch-action: none;
  overflow: hidden;
  height: calc(~"100% - 125px");
  li {
    height: 61px;
    display: flex;
    border-bottom: 1px solid #eaeaea;
    background-color: white;
  }
  img {
    height: 45px;
    width: 45px;
    margin: 8px;
  }
  .organization-name {
    height: 61px;
    line-height: 61px;
    width: calc(~"100% - 160px");
    font-size: 0.9rem;
    overflow: hidden;
  }
  .ico {
    height: 61px;
    line-height: 61px;
    width: 40px;
    color: #a4a9b2;
    text-align: center;
    font-size: 0.9rem;
  }
}

.organization-bar {
  height: 30px;
  line-height: 30px;
  font-size: 0.8rem;
  overflow-x: scroll;
  background-color: #f2f2f2;
  .organization-item {
    color: #2c84ff;
    margin: 0 5px;
  }
  p {
    color: #787878
  }
}
</style>