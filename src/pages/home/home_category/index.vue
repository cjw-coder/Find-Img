<template>
  <view class="category_list">
    <navigator v-for="item in category_list" :key="item.index" class="category_item" :url="`/pages/imgCategory/index?id=${item.id}`">
      <image :src="item.cover" mode="aspectFill"></image>
      <text>{{item.name}}</text>
    </navigator>
  </view>
</template>

<script>
export default {
    name:"home_category",
    data(){
      return{
        category_list:[]
      }
    },
    mounted(){
      uni.setNavigationBarTitle({title:"分类"})
      this.getList()
    },
    methods:{
      getList(){
        this.request({
          url:"http://157.122.54.189:9088/image/v1/vertical/category"
        }).then(res =>　{
          const result = res.res
          this.category_list = [...result.category]
        })
      }
    }
}
</script>

<style lang="scss" scoped>
  .category_list{
    display: flex;
    flex-wrap: wrap;
    .category_item{
      width:33.33%;
      height:10%;
      padding:6rpx;
      position:relative;
      image{
        height:240rpx;
      }
      text{
        position:absolute;
        bottom:8rpx;
        right:8rpx;
        color:#fff;
        font-size:36rpx;
        background:rgba(125,125,125,0.5);
        border-radius:8rpx;
      }
    }
  }
</style>