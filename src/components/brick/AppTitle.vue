<template lang="pug">
.app-title {{title}}
</template>

<script>
export default {
    props: {
        title: {
            type: String,
            required: true
        },
    },  
}

</script>

<style lang='less' scoped>
.app-title{
    background-color: white;
    padding: 0 10px;
    height: 25px;
    line-height: 25px;
    border-bottom: 1px solid #e5e5e5;
    border-top: 1px solid #e5e5e5;
    margin-top: 5px;
    color: #7e7e7e;
    font-size: 0.8rem;
}
</style>