<template>
  <scroll-view scroll-y @scrolltolower="handleScroll" class="recommend_view" v-if="isShow">
    <view class="recommend_list">
      <view class="recommend_item" v-for="item in recommend_list" :key="item.index">
        <image :src="item.thumb" mode="widthFix"></image>
      </view>
    </view>
    <view class="month_list">
      <view class="month_list_title">
        <view>{{date}}</view>
        <view>{{month_data.title}}</view>
        <view>更多 ></view>
      </view>
      <view class="month_list_content">
        <view class="month_item" v-for="item in month_list" :key="item.index">
          <image :src="item.thumb + item.rule.replace('$<Height>',260)" mode="aspectFill"></image>
        </view>
      </view>
    </view>
    <view class="hot_list">
      <view class="hot_list_title">热门</view>
      <view class="hot_list_content">
        <view class="hot_list_item" v-for="item in hot_list" :key="item.index">
          <image :src="item.thumb" mode="aspectFill"></image>
        </view>
      </view> 
    </view>
  </scroll-view>
</template>

<script>
import moment from 'moment'
export default {
    name:"home_recommend",
    data(){
      return{
        recommend_list:[],
        month_data:{},
        month_list:[],
        hot_list:[],
        isShow:false,
        params:{
          limit:30,
          order:'hot',
          skip:0
        },
        hasMore:true
      }
    },
    mounted(){
      uni.setNavigationBarTitle({title:"推荐"}),
      this.getList()
    },
    computed:{
      date(){
        return `${this.month_data.DD} / ${this.month_data.MM} 月`
      }
    },
    methods:{
      getList(){
        this.request({
          url:'http://157.122.54.189:9088/image/v3/homepage/vertical',
          data:this.params
        }).then(res => {
          const result = res.res
          if(result.vertical.length == 0){
            this.hasMore = false
          }
          if(this.recommend_list.length == 0){
            this.recommend_list = result.homepage[1].items
            this.month_data = result.homepage[2]
            this.month_data.MM = moment(this.month_data.stime).format('MM')
            this.month_data.DD = moment(this.month_data.stime).format('DD')
            this.month_list = result.homepage[2].items
            this.isShow = true
          }
          this.hot_list = [...this.hot_list,...result.vertical]
        })
      },
      handleScroll(){
        if(this.hasMore){
          this.params.skip += this.params.limit
          this.getList()
        }else{
          uni.showToast({
            title:"没有更多图片了哟~~",
            icon:"none"
          })
        }
      }
    }
}
</script>

<style lang="scss" scoped>
  .recommend_view{
    height:calc( 100vh - 72rpx);
  }
  .recommend_list{
    display: flex;
    flex-wrap: wrap;
    .recommend_item{
      width:50%;
      border:6rpx solid rgb(255,255,255);
    } 
    .recommend_item image{
      border-radius: 16rpx;
    }
  }
  .month_list{
    .month_list_title{
      display: flex;
      font-size: 32rpx;
      padding:0 14rpx;
      padding-bottom:0;
      height:65rpx;
      line-height:65rpx;
      view{
        font-weight:bold;
        flex:2;
      }
      :first-child{
        color:#d81e06;
        flex:1;
      }
      :last-child{
        flex:1;
        text-align:right;
        font-size:24rpx;
        color:#d81e06;
      }
    }
    .month_list_content{
      display: flex;
      flex-wrap: wrap;
      .month_item{
        width:33.33%;
        border:4rpx solid rgb(255,255,255);
      } 
    }
  }
  .hot_list{
    padding:6rpx;
  .hot_list_title{
    border-left:14rpx solid #d81e06;
    padding-left:8rpx;
  }
  .hot_list_content{
    display: flex;
    flex-wrap: wrap;
    .hot_list_item{
      width:33.33%;
      border:4rpx solid rgb(255,255,255);
    }
  }
}
</style>