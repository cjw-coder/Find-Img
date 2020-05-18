<template>
  <view>
      施工中...
  </view>
</template>

<script>
export default {
    name:"album",
    data(){
        return{
            params:{
                limit:30,
                order:'new',
                skip:0,
                first:0
            },
            id:-1
        }
    },
    onLoad(options){
        this.id = options.id
        this.getList()
    },
    methods:{
        getList(){
            this.request({
                url:`http://157.122.54.189:9088/image/v1/wallpaper/album/${this.id}/wallpaper`,
                data:this.params
            }).then(res => {
                const result = res.res
                console.log(result)
            })
        }
    }
}
</script>

<style>

</style>