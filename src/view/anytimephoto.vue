<template>
<div class="page container" ref="wrapper">
    <div class="loading" v-if="isShowLoading"><img src="../../static/Spinner-1s-90px.svg"></div>
        <router-link :to="{name:'newsDetail',params:{id:item.newsId}}" v-for="item in PhotoList" :key="item.newsId">
            <img :src="item.pic">
            <div>{{item.title}}</div>
        </router-link>
        <div class="bottom-text" v-show="isShowText">没有更多数据了</div>
</div>
</template>

<script type="text/ecmascript-6">
export default {
 data() {
 return {
    PhotoList:[],
    isShowLoading:false,
    isShowText:false
 }
 },
 methods:{
     getPhoto(){
        this.isShowLoading = true
        this.$axios.get(`/news/newsList.do`,{page:1,rows:10,type:7}).then(res=>{
            if (res.code == 1) {
                this.isShowLoading = false
                this.PhotoList = res.rows
                if (res.rows.length < 10) {
                    this.isShowText = true
                }
            }
        }).catch(err=>{
            this.isShowLoading = false
        })
    }
 },
 mounted () {
    this.getPhoto()
}
}
</script>

<style scoped lang="scss">
    a{
        display: inline-block;
        padding:.31rem;
        font-size: .26rem;
        width:3.1rem;
        color:#555;
        img{
            width:3.1rem;
            height:2.8rem;
        }
        div{
        display: -webkit-box;
        -webkit-box-orient: vertical;
        -webkit-line-clamp:2;
        overflow: hidden;
        }
    }
</style>
