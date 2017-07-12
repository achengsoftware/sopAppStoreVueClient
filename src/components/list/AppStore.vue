<template lang="pug">
li.app-list
    img(:src="icon")
    .info
        p.name {{neme}}
        p.detail {{detail}}
    .button.open(v-if="exist") 打开
    .button.down(v-else) 安装
</template>

<script>
export default {
    props: {
        neme: {
            type: String,
            required: true
        },
        detail: {
            type: String,
            required: true
        },
        exist: {
            type: Boolean
        },
        icon: {
            type: String,
            required: true
        }
    }
}

</script>

<style lang='less' scoped>
  .app-list{
    display: flex;
    background-color: white;
    img{
      width: 45px;
      height: 45px;
      margin: 9px;
    }
    .info{
      width: calc(~"100% - 150px");
    }
    p{
      height: 30px;
    }
    .name{
      font-size: 0.9rem;
      line-height: 33px;
    }
    .detail{
      font-size: 0.8rem;
      line-height: 30px;
      color: #7b7b7b;
    }
    .button{
      width: 60px;
      height: 30px;
      border-radius: 5px;
      margin: 16px 13px;
      text-align: center;
      line-height: 30px;
      font-size: 0.9rem;
    }
    .open{
      border: 1px solid black;
    }
    .down{
      color: white;
      background-color: #1865ff;
    }
  }
</style>