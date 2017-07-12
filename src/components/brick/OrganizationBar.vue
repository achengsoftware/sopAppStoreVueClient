<template lang="pug">
<div class="organization-bar">
    <template v-for="item in tree">
        <a>{{item.name}}</a>
        <span>></span> 
    </template>
</div>
  
</template>

<script>
export default {
    props: {
        tree: Object
    }
}

</script>

<style>
.organization-bar{
    height:45px;
    display: flex;
    line-height: 45px;
    font-size: 0.8rem;
}
.organization-bar a{
    color: #2c84ff;
    margin: 0 10px;
}
.organization-bar p{
    color: #787878
}
</style>