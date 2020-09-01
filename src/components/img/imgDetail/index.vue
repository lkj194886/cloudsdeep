<template>
  <view>
    <!--用户信息-->
    <view class="user_info">
      <view class="user_icon">
        <img :src="imgDetail.user.avatar" mode="widthFix" />
      </view>
      <view class="user_desc">
        <view class="user_name">{{imgDetail.user.name}}</view>
        <view class="user_time">{{imgDetail.cnTime}}</view>
      </view>
    </view>

    <!--图片信息-->
    <view class="high_img" @longpress="logoTime">
      <swiper-action @swiperAction="handleSwiperAction">
        <uni-popup ref="popup" type="center"><text class="iconfont icon-xiazai1" @click="download"></text></uni-popup>
        <img mode="widthFix" :src="imgDetail.thumb" />
      </swiper-action>
    </view>

    <!--点赞-->
    <view class="user_rank">
      <view class="rank">
        <text class="iconfont icondianzan">{{imgDetail.rank}}</text>
      </view>
      <view class="user_collect">
        <text class="iconfont iconshoucang">收藏</text>
      </view>
    </view>

    <!--专辑-->
    <view class="album_wrap" v-if="album.length">
      <view class="album_title">相关</view>

      <view class="album_list">
        <view class="album_time" v-for="i in album" :key="i.id">
          <view class="album_cover">
            <img :src="i.cover" mode="aspectFill" />
          </view>

          <view class="album_info">
            <view class="album_info_text">专辑</view>

            <view class="album_name">{{i.name}}</view>
            <text class="iconfont iconiconfontjiantou4"></text>
          </view>
        </view>
      </view>
    </view>

    <text class="desc_exist" v-if="exist">还没有评论哦!</text>
    <!--最热-->

    <view class="comment hot" v-if="hot.length">
      <view class="comment_title">
        <text class="iconfont iconhot1"></text>
        <text class="comment_text">最热评论</text>
      </view>

      <view class="comment_list">
        <view class="comment_item" v-for="item in hot" :key="item.id">
          <view class="comment_user">
            <view class="user_icon">
              <img :src="item.user.avatar" mode="widthFix" />
            </view>

            <view class="user_name">
              <view class="user_nicname">{{item.user.name}}</view>
              <view class="user_time">{{item.cnTime}}</view>
            </view>

            <view class="user_badge">
              <img v-for="item2 in item.user.title" :key="item2.icon" :src="item2.icon" />
            </view>
          </view>
          <view class="comment_desc">
            <view class="comment_content">{{item.content}}</view>
            <view class="comment_like">
              <text class="iconfont icondianzan">{{item.size}}</text>
            </view>
          </view>
        </view>
      </view>
    </view>

    <!--最新-->
    <view class="comment new" v-if="comment.length">
      <view class="comment_title">
        <text class="iconfont iconpinglun"></text>
        <text class="comment_text">最新评论</text>
      </view>

      <view class="comment_list">
        <view class="comment_item" v-for="item in comment" :key="item.id">
          <view class="comment_user">
            <view class="user_icon">
              <img :src="item.user.avatar" mode="widthFix" />
            </view>

            <view class="user_name">
              <view class="user_nicname">{{item.user.name}}</view>
              <view class="user_time">{{item.cnTime}}</view>
            </view>

            <view class="user_badge">
              <img
                v-for="item2 in item.user.title"
                :key="item2.icon"
                :src="item2.icon"
                mode="widthFix"
              />
            </view>
          </view>

          <view class="comment_desc">
            <view class="comment_content">{{item.content}}</view>
            <view class="comment_like">
              <text class="iconfont icondianzan">{{item.size}}</text>
            </view>
          </view>
        </view>
      </view>
    </view>
  </view>
</template>


<script>
import moment from "moment";
import swiperAction from "@/components/img/swiperAction";
import {uniPopup} from '@dcloudio/uni-ui'
moment.locale("zh-cn");
export default {
  components: {
    swiperAction,
    uniPopup
  },
  data() {
    return {
      //图片信息对象
      imgDetail: {},
      album: [],
      hot: [],
      comment: [],
      exist: false,
      //圖片的索引
      imgIndex: 0,
      // userexist:true,
    };
  },
  onLoad() {
    // console.log(getApp().globalData);
    const { imgIndex } = getApp().globalData;
    this.imgIndex = imgIndex;
    // console.log(imgIndex);
    this.getData();
  },
  methods: {
    getComments(id) {
      this.request({
        url: `http://157.122.54.189:9088/image/v2/wallpaper/wallpaper/${id}/comment`,
      }).then((res) => {
        this.album = res.res.album;

        res.res.hot.forEach(
          (v) => (v.cnTime = moment(v.atime * 1000).fromNow())
        );
        res.res.comment.forEach(
          (v) => (v.cnTime = moment(v.atime * 1000).fromNow())
        );
        if (res.res.comment.length === 0 && res.res.hot.length === 0) {
          this.exist = true;
        }
        this.hot = res.res.hot;
        this.comment = res.res.comment;
      });
    },
    handleSwiperAction(e) {
      // console.log(e.direction);
      const { imgList } = getApp().globalData;
      if (e.direction === "left" && this.imgIndex < imgList.length - 1) {
        // console.log(imgList.length-1)
        // console.log(this.imgIndex)
        this.imgIndex++;
        this.getData();
      }else{
        uni.showToast({
          title: "已经到最后一页了哦!",
          icon: "none",
        });
      }

      if (e.direction === "right" && this.imgIndex > 0) {
        this.imgIndex--;
        this.getData();
      }
      if(e.direction === "right" && this.imgIndex === 0){
           uni.showToast({
          title: "已经到第一页了哦!",
          icon: "none",
        });
      }

    },
    getData() {
      const { imgList } = getApp().globalData;

      this.imgDetail = imgList[this.imgIndex];

      this.imgDetail.cnTime = moment(this.imgDetail.atime * 1000).fromNow();

      this.getComments(this.imgDetail.id);
    },
    //弹出下载按钮
    logoTime() {
      this.$refs.popup.open()
      
    },

    //下载事件
   async download(){
      console.log("下载")
      const result=await uni.downloadFile({url:this.imgDetail.img})
      const {tempFilePath}=result[1]
      const res= await uni.saveImageToPhotosAlbum({
        filePath:tempFilePath
      })
      
      if(res[1].errMsg="saveImageToPhotosAlbum:ok"){
          this.$refs.popup.close()
          uni.showToast({
            title:"已成功保存到相册",
            icon:"none"
          })
      }
      
    },
  },
};
</script>

<style lang="scss" scoped>
img {
  width: 100%;
  height: 100%;
}

.user_info {
  display: flex;
  padding: 20rpx;
  .user_icon {
    padding: 0 20rpx;
    img {
      width: 90rpx;
      border-radius: 50%;
    }
  }

  .user_desc {
    .user_name {
      color: #000;
      font-weight: 600;
    }

    .user_time {
      color: #ccc;
      font-size: 24rpx;
      padding: 10rpx 0;
    }
  }
}

.user_rank {
  display: flex;
  height: 80rpx;
  border-bottom: 5rpx solid #eee;
  .rank {
    display: flex;
    flex: 1;
    justify-content: center;
    align-items: center;
    .iconfont {
    }
  }

  .user_collect {
    display: flex;
    flex: 1;
    justify-content: center;
    align-items: center;
    .iconfont {
    }
  }
}

.album_wrap {
  padding: 20rpx;
  .album_title {
    padding: 10rpx 0;
    font-weight: 600;
  }

  .album_list {
    .album_time {
      display: flex;
      padding: 10rpx 0;
      border-bottom: 5rpx solid #eee;
      .album_cover {
        flex: 1;
        img {
          width: 200rpx;
          height: 150rpx;
        }
      }

      .album_info {
        flex: 3;
        padding-left: 20rpx;
        position: relative;
        .album_info_text {
          width: 100rpx;
          height: 50rpx;
          background-color: $color;
          color: #fff;
          display: flex;
          justify-content: center;
          align-items: center;
        }

        .album_name {
          padding: 10rpx 0;
          color: #888;
        }
        .iconfont {
          font-size: 40rpx;
          position: absolute;
          top: 50%;
          transform: translateY(-50%);
          right: 5%;
          color: #000;
        }
      }
    }
  }
}

.desc_exist {
  display: flex;
  justify-content: center;
  margin-top: 100rpx;
  font-size: 25rpx;
}

.comment {
  .comment_title {
    padding: 15rpx;
    .iconfont {
      color: red;
      font-size: 40rpx;
    }

    .comment_text {
      font-weight: 600;
      font-size: 28rpx;
      color: #666;
      margin-left: 10rpx;
    }
  }

  .comment_list {
    .comment_item {
      //用戶信息
      border-bottom: 15rpx solid #eee;
      .comment_user {
        display: flex;
        padding: 20rpx 0;
        .user_icon {
          width: 15%;
          display: flex;
          justify-content: center;
          align-items: center;
          img {
            width: 90%;
          }
        }

        .user_name {
          flex: 1;
          justify-content: center;
          .user_nicname {
            color: #777;
          }

          .user_time {
            color: #ccc;
            font-size: 24rpx;
            padding: 5rpx;
          }
        }

        .user_badge {
          padding: 10rpx;
           display: flex;
          img {
            width: 40rpx;
            height: 40rpx;
            
          }
        }
      }

      .comment_desc {
        display: flex;
        padding: 30rpx;
        .comment_content {
          flex: 1;
          padding-left: 15%;
        }

        .comment_like {
          text-align: right;
          .icondianzan {
          }
        }
      }
    }
  }
}

.new {
  .iconpinglun {
    color: aqua !important;
  }
}
.high_img{
  .icon-xiazai1 {
    // width: 130rpx;
    // height: 130rpx;
    font-size: 180rpx;
    color: #eee;
    border: 5rpx solid #eee;
    border-radius: 50%;
    
  }
}
</style> 