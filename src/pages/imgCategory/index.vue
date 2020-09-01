<template>
  <view>
    <view class="category_tab">
      <view class="category_tab_title">
        <view class="title_inner">
          <uni-segmented-control
            :current="current"
            :values="items.map(v=>v.title)"
            @clickItem="onClickItem"
            style-type="text"
            active-color="#d4237a"
          ></uni-segmented-control>
        </view>

        <view class="iconfont iconsearch" @click="categorySearch"></view>
      </view>

      <scroll-view @scrolltolower="handleSToLower" scroll-y="true" enable-flex class="category_tab_title">
        <view  class="category_tab_content">
          <view class="cate_item" v-for="(item,index) in vertical" :key="item.id">
              <go-hot :list="vertical" :index="index" :imgId="id" :params="params" :imgType="type">
                 
            <img :src="item.thumb" mode="widthFix" />
              </go-hot>
          </view>
        </view>
      </scroll-view>

    </view>
  </view>
</template>
<script>
import { uniSegmentedControl } from "@dcloudio/uni-ui";
import goHot from "@/components/img/goHot";
export default {
  components: {
    uniSegmentedControl,
    goHot
  },
  data() {
    return {
      items: [
        { title: "最新", order: "new" },
        { title: "热门", order: "hot" },
      ],
      current: 0,
      params: {
        limit: 30,
        skip: 0,
        order: "new",
      },
      id: 0,
      vertical: [],
      hasMore:true,
      type:"classify"
    };
  },
  onLoad(options) {
    this.id = options.id;
    console.log(this.id)
    this.getList();
  },
  methods: {
    onClickItem(e) {
      if (this.current !== e.currentIndex) {
        this.current = e.currentIndex;
      }else{
          return;
      }
      this.params.order = this.items[e.currentIndex].order;
      this.params.skip=0;
      this.vertical=[]
      this.hasMore=true;
      this.getList();
    },
    getList() {
        console.log(this.params)
      this.request({
        url: `http://157.122.54.189:9088/image/v1/vertical/category/${this.id}/vertical`,
        data: this.params
      }).then((res) => {

          if(res.res.vertical.length===0){
              this.hasMore=false;
              uni.showToast({
                  title:"没有更多数据了",
                  icon:"none"
              })
          }
        this.vertical=[...this.vertical,...res.res.vertical]
      });
    },
    //加载下一页数据
    handleSToLower(){
        if(this.hasMore){
            this.params.skip+=this.params.limit
            this.getList()
        }else{
            uni.showToast({
                title:"没有更多数据了!",
                icon:"none"
            })
        }
    },
    //搜索
    categorySearch(){
        uni.showToast({
            title:"暂未开放!",
            icon:"none"
        })
    }
  },
};
</script>
<style lang="scss" scoped>
img {
  width: 100%;
  height: 100%;
}
.category_tab {
  .category_tab_title {
    position: relative;
    .title_inner {
      width: 60%;
      margin: 0 auto;
      
    }
    .iconsearch {
      position: absolute;
      top: 50%;
      transform: translateY(-50%);
      right: 10%;
    }
  }
  .category_tab_title {
    .category_tab_content {
      display: flex;
      flex-wrap: wrap;
      height: calc(100vh - 36px);
      .cate_item {
        width: 33.33%;
        border: 5rpx solid #eee;
        img {
        }
      }
    }
  }
}
</style>