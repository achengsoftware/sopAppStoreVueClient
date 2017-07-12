<template>
<div class="time-out-box">
  <div class="img-box">
    <img src="../../assets/timeOut.png"></img>
  </div>
  <p class="text">您的会话已超时</p>
  <a href="#/" class="button">重新登录</a>
</div>
</template>

<style scoped>
.text{
    text-align: center;
    color: lightseagreen;
    font-size: 1.4rem;
}
.button{
    text-align: center;
    height: 50px;
    line-height: 50px;
    border: 1px solid cadetblue;
    width: 200px;
    margin: 0 auto;
    border-radius: 5px;
    margin-top: 30px;
    background-color: deepskyblue;
    color: white;
    font-size: 1.4rem;
    display: block;
}
.img-box {
    height: 128px;
    width: 128px;
    margin: 0 auto;
    padding: 20% 0;
}
</style>