<template>
    <view @click="handleClick">
        <slot></slot>
    </view>
</template>

<script>
export default {
    name:"go_detail",
    props:{
        list:Array,
        index:Number
    },
    methods:{
        handleClick(){
           getApp().globalData.imgList = this.list
           getApp().globalData.imgIndex = this.index
           console.log('ssss')
           uni.navigateTo({
               url:"/pages/imgDetail/index"
           })
        }
    }
}
</script>

<style>

</style>