<template>
  <view class="video_play">
    <image :src="videoObj.img"></image>
      <view class="video_tool" mode="aspectFill">
        <view class="iconfont iconshengyin" @click="isMuted"></view>
        <view class="iconfont iconzhuanfa">
          <button open-type="share"></button>
        </view>
      </view>
      <view class="video_item">
        <video :src="videoObj.video" :muted="ismuted"></video>
      </view>
      <view class="download">
        <view class="download_btn" @click="handleDownload">下载高清</view>
      </view>
  </view>
</template>

<script>
export default {
    name:"videoPlay",
    onLoad(){
        console.log(getApp().globalData.video)
        this.videoObj = getApp().globalData.video
    },
    data(){
      return{
        videoObj:{},
        ismuted:false
      }
    },
    methods:{
      isMuted(){
        this.ismuted = !this.ismuted
      },
      async handleDownload(){
        await uni.showLoading({titile:"下载中..."})

        const result1 =  await uni.downloadFile({url:this.videoObj.video})
        const {tempFilePath} = result1[1]

        const result2 = await uni.saveVideoToPhotosAlbum({filePath:tempFilePath})
        
        uni.hideLoading()
        await uni.showToast({title:"下载已结束"})
      }
    }
}
</script>

<style lang="scss" scoped>
  .video_play{
    image{
      position: absolute;
      z-index:-1;
      height:100vh;
      width:100vw;
      filter:blur(16rpx);
    }
    .video_tool{
      view{
        background:rgba(0,0,0,0.5);
        border-radius:60%;
        width:100rpx;
        padding:28rpx;
        margin:24rpx;
        text-align: center;
        float:right;
        color:#fff;
        position:relative;
        button{
          position: absolute;
          top:-2rpx;
          left:-2rpx;
          width:100%;
          height:100%;
          opacity: 0;
      }
      }
    }
    .video_item{
      position:absolute;
      top:200rpx;
      left:200rpx;
      video{
        height:596rpx;
        width:362rpx;
      }
    }
    .download{
      position:absolute;
      bottom:250rpx;
      left:180rpx;
      .download_btn{
        background:rgba(0,0,0,0.5);
        color:#fff;
        font-size:30rpx;
        text-align: center;
        border-radius: 45rpx;
        border:2rpx solid rgb(255,255,255);
        width:390rpx;
        height:95rpx;
        line-height:95rpx;
      }
    }
  }
</style>