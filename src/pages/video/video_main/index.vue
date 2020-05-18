<template>
  <scroll-view scroll-y enable-flex class="mainList" @scrolltolower = "handleScroll">
      <view class="mainList_item" v-for="(item,index) in mainList" :key="index" @click = "handleclick(item)">
          <image :src="item.img" mode="aspectFill"></image>
      </view>
  </scroll-view>
</template>

<script>
export default {
    name:"video_main",
    props:{
        urlObj:{
            type:Object,
            default:{}
        }
    },
    data(){
        return{
            mainList:[],
            hasMore:true
        }
    },
    mounted(){
        this.getList()
    },
    watch:{
        urlObj(){
            this.mainList = []
            this.getList()
        }
    },
    methods:{
        getList(){
            uni.showLoading({title:"加载中..."})
            this.request({
                url:this.urlObj.url,
                data:this.urlObj.params
            }).then(res => {
                if(res.res.videowp.length == 0){
                    this.hasMore = false
                }
                this.mainList = [...this.mainList,...res.res.videowp]
                uni.hideLoading()
            })
        },
        handleScroll(){
            if(this.hasMore){
                this.urlObj.params.skip += this.urlObj.params.limit
                this.getList()
            }
        },
        handleclick(item){
            getApp().globalData.video = item
            uni.navigateTo({
                url:"/pages/videoPlay/index"
            })
        }
    }
}
</script>

<style lang="scss" scoped>
    .mainList{
        display: flex;
        flex-wrap: wrap;
        height:calc( 100vh - 72rpx );
        .mainList_item{
            width:33.33%;
            padding:6rpx;
        }
    }
</style>