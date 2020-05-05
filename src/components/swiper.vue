<template>
<div class="box">
    <swiper indicator-dots="true" indicator-color="#fff" indicator-active-color="#007AFF" autoplay="true" interval="2000" circular="true">
      <swiper-item>
      <img src="../../static/images/banner01.jpg" class="img"/>
      </swiper-item>
       <swiper-item>
      <img src="../../static/images/banner02.jpg" class="img"/>
      </swiper-item>
       <swiper-item>
      <img src="../../static/images/banner03.jpg" class="img"/>
      </swiper-item>
    </swiper>
  </div>
</template>
<script>
export default {
    
}
</script>
<style scoped>
.box{
  width: 100%;
}
swiper,swiper-item,img{
  height: 400rpx;
  width: 100%;
}
</style>
