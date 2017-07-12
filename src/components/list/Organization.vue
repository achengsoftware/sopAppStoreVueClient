<template lang="pug">
router-link.organization-list(tag="div",:to="'/Information/'+ name + '/' + text + '/'+ theEnMobile + '/'+ theDuty + '/' + theTelPhone")
  img.user-img(src='../../assets/user.png')
  .message
    p.name {{name}}
    p.text {{text}}
</template>

<script>
export default {
  name: 'page-tabbar',
  props: {
    name: {
      type: String,
      required: true
    },
    text: {
      type: String,
      required: true
    },
    enMobile: {
      type: String,
      required: true
    },
    duty: {
      type: String,
      required: true
    },
    telPhone: {
      type: String,
      required: true
    },
  },
  data () {
    return{
      theEnMobile:'未设置',
      theDuty: '未设置',
      theTelPhone: '未设置'
    }
  },
  mounted () {
    if(this.enMobile) { this.theEnMobile = this.enMobile}
    if(this.duty) { this.theDuty = this.duty}
    if(this.telPhone) { this.theTelPhone = this.telPhone}
  }
}
</script>

<style lang='less' scoped>
  .organization-list{
    display: flex;
    height: 65px;
    border-bottom: 1px solid #eaeaea;
    position: relative;
    background-color: white;
    .user-img{
      width: 45px;
      height: 45px;
      margin: 10px;
      border-radius: 5px;
    }
    .name{
      height: 25px;
      line-height: 25px;
    }
    .message{
      margin: 10px 0;
      width: calc(~'100% - 130px');
    }
    .text{
      color: #8c8c8c;
      font-size: 0.8rem;
      height: 20px;
      line-height: 20px;
    }
  }
</style>