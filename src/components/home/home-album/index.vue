<template>
  <scroll-view class="album_scorll_view" scroll-y="true" @scrolltolower="handleToLower">
    <!--轮播图-->
    <view class="album_swiper">
      <swiper autoplay="false" indicator-dots="false" circular="false">
        <swiper-item v-for="item in banner" :key="item.id">
          <image :src="item.thumb" style="width: 100%;height: 100%;" />
        </swiper-item>
      </swiper>
    </view>

    <view class="album_list">
      <navigator class="album_item" v-for="item in albumlist" :key="item.id" :url="`/pages/album/index?id=${item.id}`">
        <view class="album_img">
          <img :src="item.cover" mode="aspectFill"/>
        </view>

        <view class="album_info">
          <view class="album_name">{{item.name}}</view>
          <view class="album_desc">{{item.desc}}</view>
          <view class="album_btn">
            <view class="album_attention">关注</view>
          </view>
        </view>
      </navigator>
    </view>
  </scroll-view>
</template>
<script>
export default {
  data() {
    return {
      params: {
        limit: 30,
        order: "new",
        skip: 0,
      },
      //轮播图
      banner: [],
      albumlist: [],
      hasMore: true,
    };
  },
  mounted() {
    //修改页面标题
    uni.setNavigationBarTitle({ title: "专辑" });
    this.getList();
  },
  methods: {
    getList() {
      this.request({
        url: "http://157.122.54.189:9088/image/v1/wallpaper/album",
        data: this.params,
      }).then((res) => {
        if (this.banner.length === 0) {
          this.banner = res.res.banner;
        }
        if (res.res.album.length === 0) {
          this.hasMore = falsel;
            uni.showToast({
            title: "没有更多数据了",
            icon: "none",
          });
          return;
        }
        this.albumlist = [...this.albumlist, ...res.res.album];
      });
    },
    handleToLower() {
      if (this.hasMore) {
        this.params.skip = this.params.limit;
        this.getList();
      } else {
        uni.showToast({
          title: "没有更多数据了!",
          icon: "none",
        });
      }
    },
  },
};
</script>
<style lang="scss" scoped>
.album_scorll_view {
  height: calc(100vh - 36px);
}

.album_swiper {
  swiper {
    height: calc(750rpx / 2.3);
    image {
      height: 100%;
    }
  }
}
.album_list {
  padding: 10rpx;
  .album_item {
    padding: 10rpx 0;
    display: flex;
    border-bottom: 1rpx solid #ccc;
    .album_img {
      flex: 1;
      padding: 10rpx;
      img {
        width: 270rpx;
        height: 180rpx;
      }
    }

    .album_info {
      flex: 2;
      overflow: hidden;
      padding: 0 10rpx;
      .album_name {
        font-size: 30rpx;
        color: #000;
        padding: 10rpx 0;
      }

      .album_desc {
        padding: 10rpx 0;
        font-size: 24rpx;
        text-overflow: ellipsis;
        overflow: hidden;
        white-space: nowrap;
      }

      .album_btn {
        padding: 25rpx;
        display: flex;
        justify-content: flex-end;
        .album_attention {
          color: $color;
          border: 1rpx solid $color;
          padding: 10rpx;
        }
      }
    }
  }
}
</style>