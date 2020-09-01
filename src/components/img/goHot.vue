<template>
  <view @click="handleClick">
    <slot></slot>
  </view>
</template>
<script>
export default {
  props: [
    "list",
    "index",
    "imgId",
    "params",
    "imgType",
  ],
  onLoad() {
   
  },
  methods: {
    handleClick() {
      getApp().globalData.imgList = this.list;
      getApp().globalData.imgIndex = this.index;
      getApp().globalData.imgId=this.imgId;
      getApp().globalData.imgParams=this.params;
      getApp().globalData.imgType=this.imgType;
      
      uni.navigateTo({
        url: "/components/img/imgHot/index",
      });
      //  uni.navigateTo({
      //   url:"/components/img/imgDetail/index"
      // })
    },
  },
};
</script>
<style lang="scss" scoped>
</style>
