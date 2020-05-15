<template>
    <view class="category_tab">
        <view class="category_tab_title">
          <view class="category_tab_inner">
            <uni-segmented-control :current="current" :values="categoryList.map(v => v.title)" @clickItem="onClickItem" style-type="text" active-color="#d81e06"></uni-segmented-control>
          </view>
          <view class="iconfont iconsearch"></view>
        </view>
        <scroll-view scroll-y enable-flex class="category_tab_content" @scrolltolower="handleScroll">
          <view v-for="(item,index) in currentList" :key="index">
            <go-detail :list="currentList" :index="index">
              <image :src="item.thumb" mode="widthFix"></image>
            </go-detail>
          </view>
        </scroll-view>
    </view>
</template>

<script>
import {uniSegmentedControl} from '@dcloudio/uni-ui'
import goDetail from '../../components/go_detail'
export default {
    name:"imgCategory",
    components:{
        uniSegmentedControl,
        goDetail
    },
    data(){
      return {
        categoryList:[
          {title:"最新",order:"new"},
          {title:"热门",order:"hot"},
        ],
        params:{
          limit:30,
          skip:0,
          order:"new"
        },
        current:0,
        currentId:'',
        currentList:[],
        hasMore:true
      }
    },
    onLoad(options){
      this.currentId = options.id
      this.getList()
    },
    methods:{
      onClickItem(e){
        if(this.current != e.currentIndex){
          this.current = e.currentIndex;
        }else{
          return 
        }
        this.params.order = this.categoryList[e.currentIndex].order
        this.getList()
      },
      getList(){
        this.request({
          url:`http://157.122.54.189:9088/image/v1/vertical/category/${this.currentId}/vertical`,
          data:this.params
        }).then(res => {
          uni.showLoading({
            title:"加载中..."
          })
          const result = res.res
          this.currentList = [...this.currentList,...result.vertical]
          if(result.vertical.length == 0){
            this.hasMore = false
          }
          uni.hideLoading()
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
  .category_tab{
    .category_tab_title{
      position:relative;
      .category_tab_inner{
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
  .category_tab_content{
      display: flex;
      flex-wrap: wrap;
      height: calc(100vh - 72rpx);
      view{
        width:33.33%;
        padding:8rpx;
      }
    }
</style>