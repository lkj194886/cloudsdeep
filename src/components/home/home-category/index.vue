<template>
  <view class="home_category">

    <navigator class="cate_item"
    v-for="item in category" :key="item.id" :url='`/pages/imgCategory/index?id=${item.id}`'>
    <img :src="item.cover" mode="aspectFill">
    <view class="cate_name">{{item.name}}</view>
    </navigator>

  </view>
</template>
<script>
export default {
  mounted() {
    //修改页面标题
    uni.setNavigationBarTitle({ title: "分类" });
    this.getList()
  },
  data() {
    return {
      category:[]
    };
  },
  methods: {
    getList(){
      this.request({
        url:"http://157.122.54.189:9088/image/v1/vertical/category"
      }).then(res=>{
        this.category=res.res.category
      })
    }
  },

};
</script>
<style lang="scss" scoped>
.home_category {
  display: flex;
  flex-wrap: wrap;
  .cate_item {
    width: 100%;
    position: relative;
   
    border: 5rpx solid #eee;
    img {
      width:100%;
      
    }

    .cate_name {
      position: absolute;
      width: 100%;
      color: #fff;
      height: 80rpx;
      left: 0;
      bottom: 0;
      background-image: linear-gradient(to right top,rgba(0,0,0,.2),rgba(0,0,0,0));
      font-size: 40rpx;
      display: flex;
      align-items: center;
      padding-left: 20rpx;
    }
  }
}
</style>