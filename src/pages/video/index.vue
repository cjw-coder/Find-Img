<template>
    <view>
        <view class="video_tab">
            <view class="video_tab_title">
              <view class="video_tab_inner">
                <uni-segmented-control :current="current" :values="items.map(v => v.title)" @clickItem="onClickItem" style-type="text" active-color="#d81e06"></uni-segmented-control>
               </view>
             <view class="iconfont iconsearch"></view>
            </view>
            <view class="video_tab_content">
              <view>
                 <video-main :urlObj = "items[current]"></video-main>
              </view>
            </view>
        </view>
    </view>
</template>

<script>
import {uniSegmentedControl} from '@dcloudio/uni-ui'
import videoMain from './video_main'
export default {
    name:'video',
    components:{
      uniSegmentedControl,
      videoMain
    },
    data(){
      return {
        items:[
          {title:"推荐",url:"http://157.122.54.189:9088/videoimg/v1/videowp/featured",params:{limit:30,skip:0,order:"hot"}},
          {title:"娱乐",url:"http://157.122.54.189:9088/videoimg/v1/videowp/category/59b25abbe7bce76bc834198a",params:{limit:30,skip:0,order:"new"}},
          {title:"热门",url:"http://157.122.54.189:9088/videoimg/v1/videowp/videowp",params:{limit:30,skip:0,order:"hot"}}
        ],
        current:0
      }
    },
    methods:{
      onClickItem(e){
        if(this.current != e.currentIndex){
          this.current = e.currentIndex;
        }
      }
    }
}
</script>

<style lang="scss" scoped>
  .video_tab{
    .video_tab_title{
      position:relative;
      .video_tab_inner{
        width:60%;
        margin:0 auto;
      }
      .iconsearch{
        position:absolute;
        top:45%;
        transform:translateY(-50%);
        right:5%;
      }
    }
  }
</style>