<template>
  <scroll-view
    class="recommend_view"
    @scrolltolower="handleToLower"
    scroll-y="true"
    v-if="recommends.length>0"
  >
    <!--推薦開始-->
    <view class="recommend_wrap">
      <view class="recommend_time" v-for="item in recommends" :key="item.id">
        <img style="width: 100%;height: 100%;" mode="widthFix" :src="item.thumb" />
      </view>
    </view>

    <view class="months_wrap">
      <view class="months_title">
        <view class="months_title_info">
          <view class="months_info">
            <text>{{monthes.DD}}/</text>
            {{monthes.MM}}月
          </view>
          <view class="months_text">{{monthes.title}}</view>
        </view>
        <view class="months_title_more">更多 ></view>
      </view>
      <view class="months_content">
        <view class="months_item" v-for="(item,index) in monthes.items" :key="item.id">
          <go-detail :list="monthes.items" :index="index">
            <img
              mode="widthFix"
              style="width: 100%;height: 100%;"
              :src="item.thumb+item.rule.replace('$<Height>',360)"
            />
          </go-detail>
        </view>
      </view>
    </view>

    <view class="hots_wrap">
      <view class="hots_tile">
        <text>热门</text>
      </view>
      <view class="hots_content">
        <view class="hot_item" v-for="(item,index) in hots" :key="item.id">
          <go-hot :list="hots" :index="index" :imgType="type">
          <img :src="item.thumb" mode="widthFix" style="width: 100%;height: 100%;" />
          </go-hot>
        </view>
      </view>
    </view>
  </scroll-view>
</template>
<script>
import moment from "moment";
import goDetail from "@/components/img/goDetail";
import goHot from "@/components/img/goHot";
export default {
  components: {
    goDetail,
    goHot
  },
  data() {
    return {
      recommends: [],
      monthes: {},
      hots: [],
      params: {
        limit: 30,
        order: "hot",
        skip: 0,
      },
      hasMore: true,
      type:"hot"
    };
  },
  mounted() {
    this.getList();

    //修改页面标题
    uni.setNavigationBarTitle({ title: "首页" });
  },
  methods: {
    handleToLower() {
      if (this.hasMore) {
        this.params.skip += this.params.limit;
        this.getList();
      } else {
        uni.showToast({
          title: "没有数据了",
          icon: "none",
        });
      }
    },

    getList() {
      this.request({
        url: "http://157.122.54.189:9088/image/v3/homepage/vertical",
        data: this.params,
      }).then((res) => {
        if (res.res.vertical.length === 0) {
          this.hasMore = false;
          uni.showToast({
            title: "没有更多数据了",
            icon: "none",
          });
          return;
        }

        if (this.recommends.length === 0) {
          //推荐模块数据
          this.recommends = res.res.homepage[1].items;
          //月份模块数据
          this.monthes = res.res.homepage[2];
          this.monthes.MM = moment(this.monthes.stime).format("MM");
          this.monthes.DD = moment(this.monthes.stime).format("DD");
        }
        //热门模块数据
        this.hots = [...this.hots, ...res.res.vertical];
        
      });
    },
  },
};
</script>
<style lang="scss" scoped>
.recommend_view {
  height: calc(100vh - 36px);
}
.recommend_wrap {
  display: flex;
  flex-wrap: wrap;
  .recommend_time {
    width: 50%;
    border: 5rpx solid #fff;
  }
}
.months_wrap {
  .months_title {
    display: flex;
    justify-content: space-between;
    padding: 20rpx;
    .months_title_info {
      color: $color;
      font-size: 30rpx;
      font-weight: 600;
      display: flex;
      .months_info {
        text {
          font-size: 36rpx;
        }
      }

      .months_text {
        font-size: 34rpx;
        color: #666;
        margin-left: 30rpx;
      }
    }

    .months_title_more {
      font-size: 24rp;
      color: $color;
    }
  }

  .months_content {
    display: flex;
    flex-wrap: wrap;
    .months_item {
      width: 33.33%;
      border: 5rpx solid #fff;
    }
  }
}
.hots_wrap {
  .hots_tile {
    padding: 20rpx;
    text {
      border-left: 15rpx solid $color;
      padding-left: 20rpx;
      font-size: 34rpx;
      font-weight: 600;
    }
  }

  .hots_content {
    display: flex;
    flex-wrap: wrap;
    .hot_item {
      width: 33.33%;
      border: 5rpx solid #fff;
      img {
      }
    }
  }
}
</style>