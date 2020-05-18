<template>
    <view class="img_detail">
        <view class="user_info">
            <image src="/static/icon/user.png" mode="aspectFill"></image>
            <view>
                <text>{{imgDetail.user.name || "匿名用户"}}</text>
                <text>{{imgDetail.cnTime}}</text>
            </view>
        </view>
        <view class="high_img">
            <swiper-action @handleSwiper="handleSwiper">
                 <image :src="imgDetail.preview" mode="widthFix"></image>
            </swiper-action>
        </view>
        <view class="user_rank">
            <text class="iconfont icondianzan">{{imgDetail.rank}}</text>
            <text class="iconfont iconshoucang">收藏</text>
        </view>
        <view class="about" v-if="album.length">
            <view>相关</view>
            <view class="about_content">
                <image :src="album[0].cover" mode="widthFix"></image>
                <view>
                    <view>专辑</view>
                    <text>{{album[0].desc}}</text>
                </view>
            </view>
        </view>
        <view class="hot" v-if="hot.length">
            <view class="iconfont iconhot1">最热评论</view>
            <view class="comment_list" v-for="item in hot" :key="item.index">
                <view>
                    <image :src="item.user.avatar" mode="widthFix"></image>
                    <view>
                        <text class="name">{{item.user.name}}</text>
                        <text>{{item.cnTime}}</text>
                    </view>
                    <text class="iconfont icondianzan">{{item.size}}</text>
                </view>
                <text class="comment">{{item.content}}</text>
            </view>
        </view>
        <view class="comment" v-if="comment.length">
            <view class="iconfont iconpinglun">最新评论</view>
            <view class="comment_list" v-for="item in comment" :key="item.index">
                <view>
                    <image :src="item.user.avatar" mode="widthFix"></image>
                    <view>
                        <text class="name">{{item.user.name}}</text>
                        <text>{{item.cnTime}}</text>
                    </view>
                    <text class="iconfont icondianzan">{{item.size}}</text>
                </view>
                <text class="comment">{{item.content}}</text>
            </view>
        </view>
        <view class="download_bar" @click="handleDownload">
            下载图片
        </view>
    </view>
</template>

<script>
import moment from 'moment'
import swiperAction from '../../components/swiper_action'
moment.locale('zh-cn')
export default {
    name:"imgDetail",
    data(){
        return{
            imgDetail:{},
            album:[],
            hot:[],
            comment:[],
            currentIndex:0
        }
    },
    onLoad(){
        const {imgList,imgIndex} = getApp().globalData
        this.currentIndex = imgIndex
        this.getData()
    },
    components:{
        swiperAction
    },
    methods:{
        getComment(id){
            this.request({
                url:`https://service.picasso.adesk.com/v2/wallpaper/wallpaper/${id}/comment`
            }).then(res => {
                const result = res.res
                console.log(result)
                result.hot.forEach(v => v.cnTime = moment(v.atime * 1000).fromNow())
                result.comment.forEach(v => v.cnTime = moment(v.atime * 1000).fromNow())
                
                this.album = result.album
                this.hot = result.hot
                this.comment = result.comment
            })
        },
        getData(){
            const {imgList,imgIndex} = getApp().globalData
            this.imgDetail = imgList[this.currentIndex]
            this.imgDetail.cnTime = moment(this.imgDetail.atime * 1000).fromNow()
            this.getComment(this.imgDetail.id)
        },
        handleSwiper(direction){
            const {imgList,imgIndex} = getApp().globalData
            if(direction === "left" && this.currentIndex < imgList.length - 1){
                this.currentIndex++
                this.getData()
            }else if(direction === "right" && this.currentIndex > 0){
                this.currentIndex--
                this.getData()
            }else{
                uni.showToast({
                    title:"没有更多了哟~~",
                    icon:"none"
                })
            }
        },
        async handleDownload(){
            await uni.showLoading({
                title:"下载中..."
            })

            const result1 = await uni.downloadFile({url:this.imgDetail.img})
            const {tempFilePath} = result1[1]

            const result2 = await uni.saveImageToPhotosAlbum({filePath:tempFilePath})

            uni.hideLoading()
            await uni.showToast({
                title:"下载已结束"
            })
        }
    }
}
</script>

<style lang="scss" scoped>
    .img_detail{
        padding-bottom:100rpx;
        .user_info{
            display: flex;
            height:100rpx;
            margin:30rpx;
            image{
                border-radius: 100%;
                height:100%;
                flex:1;
            }
            view{
                flex:6;
                padding:20rpx 20rpx 0 20rpx;
                display: flex;
                flex-direction: column;
                text:last-child{
                    color:rgba(125,125,125,0.5)
                }
            }
        }
        .user_rank{
            display: flex;
            text{
                flex:1;
                text-align: center;
                line-height:100rpx;
            }
        }
        .about{
            padding:20rpx;
            border-top:2rpx solid rgba(125,125,125,0.2);
            border-bottom:2rpx solid rgba(125,125,125,0.2);
            .about_content{
                display: flex;
                justify-content: space-around;
                padding:10rpx;
                image{
                    flex:1;
                }
                view{
                    flex:2;
                    display: flex;
                    flex-direction: column;
                    padding:10rpx 20rpx;
                    view{
                        background:#fa9e8c;
                        color:#fff;
                        width:80rpx;
                        padding:5rpx;
                        float:right;
                        flex:1;
                        text-align:center;
                    }
                    text{
                        flex:2
                    }
                }
            }
        }
        .comment_list{
            display: flex;
            flex-direction: column;
            justify-content: space-around;
            border-top:2rpx solid rgba(125,125,125,0.2);
            view{
                display: flex;
                padding:20rpx;
                image{
                    flex:1;
                }
                view{
                    flex:7;
                    padding:5rpx 20rpx;
                    display: flex;
                    flex-direction: column;
                    justify-content: center;
                    color:rgba(125,125,125,0.8);
                }
            }
            .comment{
                padding-left:115rpx;
                font-weight: bold;
                line-height:65rpx;
            }
        }
        .download_bar{
            position:fixed;
            bottom:0;
            background:#fa9e8c;
            padding:20rpx;
            border:10rpx solid #fff;
            width:100%;
            color:#fff;
            text-align: center;

        }
    }
</style>