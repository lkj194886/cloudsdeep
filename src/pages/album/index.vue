<template>
  <view>
    <!--专辑背景-->
    <view class="album_bg">
      <img :src="album.cover" mode="widthFix" />
      <view class="album_info">
        <view class="album name">{{album.name}}</view>
        <view class="album_btn">关注专辑</view>
      </view>
    </view>

    <!--作者详情-->
    <view class="album_author">
      <view class="album_author_info">
        <img mode="widthFix" :src="album.user.avatar" />
        <view class="author_name">{{album.user.name}}</view>
      </view>
      <view class="album_author_desc">
        <text>{{album.desc}}</text>
      </view>
    </view>

    <!--专辑列表-->
    <view class="album_list">
      <view class="album_item" v-for="(item,index) in wallpaper" :key="item.id">
        <go-detail :list="wallpaper" :index="index">
          <img mode="aspectFill" :src="item.thumb+item.rule.replace('$<Height>',360)" />
        </go-detail>
      </view>
    </view>
  </view>
</template>
<script>
import goDetail from "@/components/img/goDetail";
export default {
  components: {
    goDetail,
  },
  data() {
    return {
      params: {
        limit: 30,
        order: "new",
        skip: 0,
        first: 1,
      },
      id: -1,
      album: {},
      wallpaper: [],
      hasMore: true,
    };
  },
  onLoad(options) {
    this.id = options.id;
    // this.id = "5e5cf679e7bce739db1281e4";
    this.getList();
  },
  onReachBottom() {
    if (this.hasMore) {
      this.params.first = 0;
      this.params.skip += this.params.limit;
      this.getList();
    } else {
      uni.showToast({
        title: "没有更多数据了",
        icon: "none",
      });
    }
  },
  methods: {
    getList() {
      this.request({
        url: `http://157.122.54.189:9088/image/v1/wallpaper/album/${this.id}/wallpaper`,
        data: this.params,
      }).then((res) => {
        if (Object.keys(this.album).length === 0) {
          this.album = res.res.album;
        }

        if (res.res.wallpaper.length === 0) {
          this.hasMore = false;
          uni.showToast({
            title: "没有更多数据了",
            icon: "none",
          });
          return;
        }

        this.wallpaper = [...this.wallpaper, ...res.res.wallpaper];
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

.album_bg {
  position: relative;
  img {
  }

  .album_info {
    position: absolute;
    width: 100%;
    left: 0;
    bottom: 0;
    display: flex;
    justify-content: space-between;
    height: 80rpx;
    align-items: center;
    color: #fff;
    padding: 0 15rpx;
    .album.name {
      font-size: 30rpx;
      padding: 0 15rpx;
    }

    .album_btn {
      background-color: $color;
      width: 152rox;
      height: 60rpx;
      display: flex;
      justify-content: center;
      align-items: center;
      border-radius: 10rpx;
    }
  }
}

.album_author {
  padding: 10rpx;
  .album_author_info {
    padding: 10rpx 0;
    display: flex;
    img {
      width: 80rpx;
    }

    .author_name {
      font-size: 35rpx;
      color: #000;
      margin-left: -left;
    }
  }

  .album_author_desc {
    padding: 20rpx;
  }
}

.album_list {
  display: flex;
  flex-wrap: wrap;
  .album_item {
    width: 50%;
    border: 3rpx solid #fff;
    img {
      height: 250rpx;
    }
  }
}
</style>