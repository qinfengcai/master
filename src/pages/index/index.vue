<template>
  <div class="homeContainer">
    <!-- 授权登录部分 -->
    <div v-if="isHide" class="isLogin">
      <div>
        <img src="../../../static/images/logo.jpg" />
      </div>
      <div>
        <span>申请获取以下权限</span>
      </div>
      <div>
        <span>获得你的公开信息(昵称,头像,地址等)</span>
      </div>
      <button type="primary" open-type="getUserInfo" @getuserinfo="bindGetUserInfo">授权登录</button>
    </div>
    <!-- 主页部分 -->
    <div v-else>
      <!-- 轮播图 -->
      <Swiper></Swiper>
      <!-- 分类 -->
      <div class="itemWrap">
        <div class="item">
          <div class="item-content" @click="hanleClick1">
            <img src="../../../static/images/active1.png" />
            <span>活动</span>
          </div>
        </div>
        <div class="item">
          <div class="item-content" @click="hanleClick2">
            <img src="../../../static/images/active2.png" />
            <span>问卷</span>
          </div>
        </div>
        <div class="item">
          <div class="item-content" @click="hanleClick3">
            <img src="../../../static/images/active3.png" />
            <span>夏令营</span>
          </div>
        </div>
        <div class="item">
          <div class="item-content" @click="hanleClick3">
            <img src="../../../static/images/active4.png" />
            <span>课程</span>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import Swiper from "../../components/swiper";
import Store from "../../utils/store"; // 封装的本地缓存方法
export default {
  name: "index",
  data() {
    return {
      // 是否显示授权页面
      isHide: false
    };
  },
  components: {
    Swiper
  },
  methods: {
    hanleClick1() {
      wx.navigateTo({
        url: "../active/main"
      });
    },
    hanleClick2() {
      wx.reLaunch({
        url: "../asked/main"
      });
    },
    hanleClick3() {
       wx.showModal({
        title: '提示',
        content: '正在努力开发中，敬请期待~',
        success (res) {
          if (res.confirm) {
            console.log('用户点击确定')
          } else if (res.cancel) {
            console.log('用户点击取消')
          }
        }
      })
    },
    // 是否授权登录
    isLogin() {
      let _this = this;
      wx.getSetting({
        success(res) {
          // 用户是否已经授权登录
          if (res.authSetting["scope.userInfo"]) {
            console.log("用户授权了");
          } else {
            console.log("用户没有授权");
            // 显示授权页面
            _this.isHide = !_this.isHide;
            //如果用户没有授权登录过，就要通过wx.login获取用户标识符openId
            _this.getOpenId();
          }
        }
      });
    },
    //用户信息授权
    bindGetUserInfo(e) {
      let userMsg = e.mp.detail.userInfo;
      if (userMsg) {
        //用户按了确定按钮
        // 切换隐藏页面
        this.isHide = !this.isHide;
        // 同时将用户信息和openId数据一起返回给后台
        let openid = Store.getItem("openid");
        console.log(openid);
      } else {
        //用户按了取消按钮
        wx.showModal({
          title: "警告",
          content: "您点击了拒绝授权，将无法进入小程序，请授权之后再进入！！！",
          showCancel: false, // 不显示取消按钮
          confirmText: "返回授权", // 确认按钮的文字
          success(res) {
            if (res.confirm) {
              console.log("用户点击了返回授权");
            }
          }
        });
      }
    },
    // 获取openId,将openId存入本地缓存
    getOpenId() {
      let _this = this;
      wx.login({
        success(res) {
          if (res.code) {
            //发起网络请求
            _this.$fly
              .post(_this.$api.openId, {
                code: res.code
              })
              .then(res => {
                // 将openId存储到本地缓存
                let id = res.data.data.openid;
                Store.setItem("openid", id);
              });
          } else {
            console.log("登录失败！" + res.errMsg);
          }
        }
      });
    },
    // 开启分享
    startShare() {
      wx.showShareMenu({
        withShareTicket: true
      });
    }
  },
  // 分享功能 ---要写在methods外面---独立事件
  onShareAppMessage(res) {
    return {
      title: "一起搞活动",
      path: "/pages/index/main"
    };
  },
  onLoad() {
    this.isLogin();
    // 开启页面分享
    this.startShare();
  }
};
</script>

<style scoped>
.homeContainer {
  background-color: #eeeeee;
  position: fixed;
  height: 100%;
  width: 100%;
}
.itemWrap {
  width: 100%;
  display: flex;
  flex-wrap: wrap;
  margin-top: 30rpx;
}
.item {
  width: 50%;
  padding: 0rpx 14rpx 0rpx 14rpx;
  box-sizing: border-box;
  height: 350rpx;
  margin-top: 14rpx;
}
.item-content {
  display: flex;
  flex-direction: column;
  background-color: #fff;
  width: 100%;
  height: 100%;
  border: 1px solid rgba(74, 255, 255, 0.4);
  padding: 10rpx;
  box-sizing: border-box;
  text-align: center;
}
.item-content > img {
  width: 100%;
  height: 300rpx;
}
.item-content > span {
  font-size: 32rpx;
}
/* 授权登录 */
.isLogin {
  margin-top: 100rpx;
  text-align: center;
}
.isLogin img {
  width: 200rpx;
  height: 100rpx;
  margin-bottom: 50rpx;
}
.isLogin > div:nth-child(2),
.isLogin > div:nth-child(3) {
  font-size: 32rpx;
  height: 60rpx;
  line-height: 60rpx;
}
.isLogin > div:nth-child(3) {
  color: #b3b3b3;
  margin-bottom: 30rpx;
}
.isLogin > button {
  width: 70%;
}
</style>