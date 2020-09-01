<template>
  <view>
    <swiper-action @swiperAction="handleSwiperAction">
      <view class="shouimg" @longpress="logoTime">
        <uni-popup ref="popup" type="center">
          <text class="iconfont icon-xiazai1" @click="download"></text>
        </uni-popup>
        <img :src="imgDetail.img" mode="aspectFill" />
      </view>
    </swiper-action>
  </view>
</template> 
<script>
import swiperAction from "@/components/img/swiperAction";
import { uniPopup } from "@dcloudio/uni-ui";

export default {
  components: {
    swiperAction,
    uniPopup,
  },
  data() {
    return {
      imgDetail: [],
      // xiazai: false,
      imgIndex: 0,
      params: {
        limit: 30,
        order: "hot",
        skip: 0,
      },
    };
  },
  onLoad() {
    console.log(getApp().globalData);
    const { imgList, imgIndex } = getApp().globalData;
    this.imgIndex = imgIndex;
    uni.setNavigationBarTitle({ title: `${imgIndex + 1}/${imgList.length}` });
    this.getData();
  },
  methods: {
    //弹出下载按钮
    logoTime() {
      this.$refs.popup.open();
    },

    //下载事件
    async download() {
      console.log("下载");
      const result = await uni.downloadFile({ url: this.imgDetail.img });
      const { tempFilePath } = result[1];
      const res = await uni.saveImageToPhotosAlbum({
        filePath: tempFilePath,
      });

      if ((res[1].errMsg = "saveImageToPhotosAlbum:ok")) {
        this.$refs.popup.close();
        uni.showToast({
          title: "已成功保存到相册",
          icon: "none",
        });
      }
    },

    //手势滑动事件
    handleSwiperAction(e) {
      // e.stopPropagation()
      const { imgList } = getApp().globalData;
      if (e.direction === "left" && this.imgIndex < imgList.length - 1) {
        this.imgIndex++;
        this.getData();
      }

      if (e.direction === "right" && this.imgIndex > 0) {
        this.imgIndex--;
        this.getData();
      }
      if (e.direction === "right" && this.imgIndex === 0) {
        uni.showToast({
          title: "已经到第一页了哦!",
          icon: "none",
        });
      }
      if (e.direction === "left" && this.imgIndex + 1 === imgList.length - 1) {
        this.getData();
      }
    },
    getData() {
      const { imgList } = getApp().globalData;
      const { imgId } = getApp().globalData;
      const { imgParams } = getApp().globalData;
      const { imgType } = getApp().globalData;
      console.log(imgType);
      if (this.imgIndex === imgList.length - 1) {

        //查看图片(图片分类)
        if (imgType === "classify") {
          this.params.skip += this.params.limit;
          this.params.order = imgParams.order;
          this.request({
            url: `http://157.122.54.189:9088/image/v1/vertical/category/${imgId}/vertical`,
            data: this.params,
          }).then((res) => {
            if (res.res.vertical.length === 0) {
              uni.showToast({
                title: "已经到最后一页了哦!",
                icon: "none",
              });
              return;
            }

            getApp().globalData.imgList = [
              ...getApp().globalData.imgList,
              ...res.res.vertical,
            ];
          });
        }

        //查看图片(主页热门)
        if (imgType === "hot") {
          this.params.skip += this.params.limit;
          this.params.order="hot"
          this.request({
            url: "http://157.122.54.189:9088/image/v3/homepage/vertical",
            data: this.params,
          }).then((res) => {
            if (res.res.vertical.length === 0) {
              uni.showToast({
                title: "已经到最后一页了哦!",
                icon: "none",
              });
              return;
            }

            getApp().globalData.imgList = [
              ...getApp().globalData.imgList,
              ...res.res.vertical,
            ];
          });
        }
      }

      this.imgDetail = imgList[this.imgIndex];
      uni.setNavigationBarTitle({
        title: `${this.imgIndex + 1}/${imgList.length}`,
      });
    },
  },
};
</script>
<style lang="scss" scoped>
img {
  width: 100%;
  height: 100%;
}
.shouimg {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.3);
  .icon-xiazai1 {
    // width: 130rpx;
    // height: 130rpx;
    font-size: 180rpx;
    color: #eee;
    border: 5rpx solid #eee;
    border-radius: 50%;
  }
  img {
  }
}
.exit {
  font-size: 50rpx;
}
</style>