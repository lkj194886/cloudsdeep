<template>
  <view @click="handleClick">
    <slot></slot>
  </view>
</template>
<script>
export default {
  props: {
    list: Array,
    index: Number
  },
  data() {
    return {};
  },
  methods:{
    handleClick(){
      getApp().globalData.imgList=this.list
      getApp().globalData.imgIndex=this.index
      uni.navigateTo({
        url:"/components/img/imgDetail/index"
      })
    }
  }
};
</script>
<style lang="scss" scoped>
</style>>
