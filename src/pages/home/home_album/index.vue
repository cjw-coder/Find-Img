<template>
  <scroll-view scroll-y @scrolltolower="handleScroll" class="album_view" v-if="isShow">
    <swiper autoplay indicator-dots circular>
      <swiper-item v-for="item in banner_list" :key="item.index">
        <image mode="widthFix" :src="item.thumb"></image>
      </swiper-item>
    </swiper>
    <view class="album_list">
      <view class="album_item" v-for="item in album_list" :key="item.index">
        <image :src="item.cover"></image>
        <view class="album_content">
          <text>{{item.name}}</text>
          <view class="album_info">
            <text>{{item.desc}}</text>
            <view class="btn"> + 关注</view>
          </view>
        </view>
      </view>
    </view>
  </scroll-view>
</template>

<script>
export default {
    name:"home_album",
    mounted(){
      uni.setNavigationBarTitle({title:"专辑"})
      this.getList()
    },
    data(){
      return{
        album_list:[],
        banner_list:[],
        isShow:false,
        params:{
          limit:30,
          order:'new',
          skip:0
        },
        hasMore:true
      }
    },
    methods:{
      handleScroll(){
        if(this.hasMore){
          this.params.skip += this.params.limit
          this.getList()
        }else{
          uni.showToast({
            title:'没有更多了哟~~',
            icon:"none"
          })
        }
      },
      getList(){
        this.request({
          url:"https://service.picasso.adesk.com/v1/wallpaper/album",
          data:this.params
        }).then((res)=>{
          const result = res.res
          if(result.album.length == 0){
            this.hasMore = false
          }
          if(this.banner_list.length == 0){
            this.banner_list = result.banner
            this.isShow = true
          }
          this.album_list = [...this.album_list,...result.album]
        })
      }
    }
}
</script>

<style lang="scss" scoped>
  .album_view{
    height:calc( 100vh - 72rpx);
    swiper{
      height:calc( 750rpx / 2.3);
      image{
        height:100%;
      }
    }
    .album_item{
      height:200rpx;
      padding:16rpx;
      border-bottom:2rpx solid rgba(125,125,125,0.2);
      display: flex;
      image{
        flex:1;
        height:100%;
      }
      .album_content{
        flex:2;
        padding:0 20rpx;
        font-size:28rpx;
        color:rgb(0,0,0);
        font-weight: bold;
      }
      .album_info{
        height:100%;
        display: flex;
        flex-direction: column;
        justify-content: space-between;
        color:rgb(125,125,125);
        font-size:20rpx;
        position: relative;
      }
      .btn{
        width:100rpx;
        padding:10rpx;
        border:2rpx solid #d81e06;
        color:#d81e06;
        font-size:20rpx;
        font-weight: normal;
        position:absolute;
        right:0;
        bottom:34rpx;
      }
    }
  }
</style>