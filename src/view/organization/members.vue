<template>
<div class="page container">
    <div class="loading" v-if="isShowLoading"><img src="../../assets/Spinner-1s-90px.svg"></div>
        <mt-loadmore :top-method="loadTop" :bottom-method="loadBottom" :bottom-all-loaded="allLoaded" ref="loadmore" :autoFill="isAutoFill">
            <div class="userlist"  ref="wrapper">
                <div class="item" v-for="item in userList" :key="item.id">
                    <img :src="item.header" class="header-img">
                    <div class="username">{{item.username}}</div>
                    <div class="branch">{{item.branchName}}</div>
                </div>
            </div>
        </mt-loadmore>
    <div class="bottom-text" v-show="isShow">没有更多数据了</div>
</div>
</template>
<script type="text/ecmascript-6">
export default {
data() {
return {
    userData:{
            select_branch:"",
            user_id:localStorage.getItem("token"),
            page:1,
            row:10
    },
    userList:[],
    isShow:false,
    isShowLoading:false,
    //可以进行上拉
    allLoaded: false,
    //是否自动触发上拉函数
    isAutoFill: false,
    wrapperHeight: 0
}
},
methods: {
    // 下拉刷新
        loadTop() {
        this.loadFrist();
        },
        // 上拉加载
        loadBottom() {
        this.loadMore();
        },
        // 下拉刷新加载
        loadFrist() {
        const {id} = this.$route.query
        this.userData.select_branch = id
        this.userData.page = 1
        this.$axios.get(`/nationComment/userList.do`,this.userData).then(res => {
            this.allLoaded = false; // 可以进行上拉
            this.userList = res.rows
            this.loadMore()      
            this.$refs.loadmore.onTopLoaded();
            }).catch(error => {
            console.log(error);
        });
    },
        // 加载更多
    loadMore() {
        let type = this.$route.query.name 
        this.userData.page++;
        this.$axios.get(`/nationComment/userList.do`,this.userData)
        .then(res => {
            // concat数组的追加
            this.userList = this.userList.concat(res.rows);
            if (res.rows.length < 10) {
                this.allLoaded = true; // 若数据已全部获取完毕
                this.isShow = true
            }
            this.$refs.loadmore.onBottomLoaded();
        })
    },
    getUserList(){
        const {id} = this.$route.query
        this.userData.select_branch = id
        this.isShowLoading = true
        this.$axios.get(`/nationComment/userList.do`,this.userData).then(res=>{
            if (res.code == 1) {
                this.userList = res.rows;
                this.isShowLoading = false  
                this.loadMore()      
            }else{
                this.isShowLoading = false;
            }
        })
    }
},
created () {
    this.getUserList()
}
}
</script>

<style scoped lang="scss">
.page{
    font-size: 14px;
}
.userlist{
    .item{
    display: flex;
    height: 1.01rem;
    border-bottom: 1px solid #ccc;
    color:#555;
    justify-content: space-between;
    .header-img{
        width: .61rem;
        height: .61rem;
        border-radius: 50%;
        padding:10px;
    }
    .username{
        font-size: 16px;
        line-height: 1.01rem;
    }
    .branch{
        font-size: 14px;
        line-height: 1.01rem;
        padding-right: .1rem;
    }
}
}

</style>
