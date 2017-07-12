<template lang="pug">
.change-password-box
  TitleBar(title='更改密码',leftIcon="flase")
  .title 当前密码
  input(v-model="oldPassword", type="text")
  .title 新密码
  input(v-model="password", type="text")
  .title 确认密码
  input(v-model="repeatPassword", type="text")
  .prompt 建议:密码长度为8-16位，至少有数字、字母或符号的两种组合，字母区分大小写
  .button(v-on:click="verification") 确定
  Toast
</template>


<script>
import TitleBar from '../brick/Title'
import { Order } from '../Order.js'
import Toast from '../brick/Toast'
import { DATA, CHANNEL, log } from "../method.js"
export default {
  components: {
    TitleBar,
    Toast
  },
  methods: {
    //更改密码验证
    verification () {
      const _this = this
      if (!this.oldPassword || !this.password || !this.repeatPassword) { Order.$emit('Toast', '当前密码不正确'); return null; } //密码验证
      if (this.password !== this.repeatPassword) { Order.$emit('Toast', '密码不一致'); return null; } //密码验证
      //更改密码信号监听
      Order.$once('changedPwd', function (message) {
        switch (message.code) {
          case 543: Order.$emit('Toast', '当前密码不正确'); break;
          case 542: Order.$emit('Toast', '新密码不合法'); break;
          case 541: Order.$emit('Toast', '用户不存在'); break;
          case 1304: Order.$emit('Toast', '修改失败'); break;
          case 0: Order.$emit('Toast', '修改成功');
            CHANNEL.loginout()
            window.location.href = `#/Quit/${DATA.org.enname}`;
            setTimeout(() => {
              location.reload();
            }, 1000);
            break;
          default: Order.$emit('Toast', `遇到错误:${Data.code}`);
        }
      })
      const data = { oldPwd: _this.oldPassword, newPwd: _this.password }
      CHANNEL.changedPwd(JSON.stringify(data))
    }
  },
  data () {
    return {
      oldPassword: "",
      password: "",
      repeatPassword: ""
    }
  },
  watch: {
    oldPassword (val) {
      this.oldPassword = this.oldPassword.replace(/[^\w\.\/]/ig, '')
    },
    password (val) {
      this.password = this.password.replace(/[^\w\.\/]/ig, '')
    },
    repeatPassword (val) {
      this.repeatPassword = this.repeatPassword.replace(/[^\w\.\/]/ig, '')
    }
  },
}
</script>

<style lang='less' scoped>
.change-password-box {
  .title {
    height: 30px;
    line-height: 30px;
    background-color: #f4f4f4;
    font-size: 12px;
    color: grey;
    padding: 0 10px;
  }
  input {
    width: 100%;
    height: 30px;
    border: none;
    padding: 0 10px;
    font-size: 1rem;
  }
  .button {
    width: 160px;
    background-color: cadetblue;
    text-align: center;
    height: 40px;
    line-height: 40px;
    margin: 0 auto;
    border-radius: 5px;
    color: white;
    margin-top: 50px;
  }
  .prompt {
    margin: 10px;
    font-size: 13px;
    color: cadetblue;
  }
}
</style>