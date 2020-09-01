<template>
      <view @touchstart="handeltouchstart"
    @touchend="handeltouchend">
        <slot></slot>
    </view>
</template>
<script>
export default {
    data() {
        return {
            //按下的时间
            startTime:0,
            //按下的坐标
            startX:0,
            startY:0
        }
    },
    methods:{
        //按下屏幕事件
        handeltouchstart(event){
            // console.log("按下")
            // console.log(event.changedTouches[0].clientX);
            // console.log(event.changedTouches[0].clientY);
            this.startTime=Date.now()
            this.startX=event.changedTouches[0].clientX;
            this.startY=event.changedTouches[0].clientX;
        },
        //离开屏幕事件
        handeltouchend(event){
            // console.log("离开")
            // console.log(event.changedTouches[0].clientX);
            // console.log(event.changedTouches[0].clientY);
            const endTime=Date.now();
            const endstarX=event.changedTouches[0].clientX;
            const endstarY=event.changedTouches[0].clientY;
            //判断时间
            if(endTime-this.startTime>4000){
                return;
            }
            //滑动方向
            let direction=""
            //判断方向
            if(Math.abs(endstarX-this.startX)>50){
                direction=endstarX-this.startX>0?"right":"left";
            }else{
                return;
            }
            // console.log(direction)
            this.$emit("swiperAction",{direction})
        }
    }
}
</script>
<style lang="scss" scoped>
view{
    width: 100%;
    height: 100%;
}
</style>