<template>
  <div class="twolunbo">
    <img :src="bgimg" alt="" class="bgImg2" />
    <div class="swiper-fz-content">
      <div class="swiper-fz" id="swiperfz">
        <div
          class="swiper-fz-item"
          v-for="(item, index) in swiperList"
          :key="index"
          @mouseenter="mouseEnter(index)"
          @mouseleave="mouseLeave"
          :class="[
            { 'swiper-fz-item': true },
            { activeClass: activeSwiper == index },
            { twoClass: activeSwiper==index-1 || activeSwiper==index+1},
            { otherclass: activeSwiper!=index-1 && activeSwiper!=index+1 && activeSwiper != index}
          ]"
        >
          <!-- @click="change(index)" -->
          <!-- 
         -->
          <div class="content-fz">
            <div>{{ item.title }}</div>
            <div v-if="activeSwiper == index">{{ item.content }}</div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
// import {aboutasList} from '@/api/article'
export default {
  props: ["twoData"],
  data() {
    return {
      bgimg: require("@/assets/img/diqiu.png"),
      swiperList: [],
      activeSwiper: 4,
      timer: null,
      timer2: null,
      timer3: null,
      duration: "1s",
      swiperHeight:84,
    };
  },
  mounted() {
    clearInterval(this.timer);
    this.$nextTick(()=>{
      this.init();
      this.play();
    })
    window.addEventListener("visibilitychange",this.visibilitychange);
  },
  watch: {
    twoData(newVal, oldVal) {
      this.twoData
        .slice(this.twoData.length - 4, this.twoData.length)
        .forEach((item) => this.swiperList.push(item));
      this.twoData.forEach((item) => this.swiperList.push(item));
      this.twoData.slice(0, 4).forEach((item) => this.swiperList.push(item));
    },
  },
  beforeDestroy() {
    this.timer && clearInterval(this.timer);
    this.timer2 && clearInterval(this.timer2);
    this.timer3 && clearInterval(this.timer3);
    window.removeEventListener("visibilitychange",this.visibilitychange)
  },
  methods: {
    visibilitychange() {
      if (!document.hidden) {
        this.play();
      } else {
        clearInterval(this.timer);
        this.timer = null;
      }
    },
    init() {
      if (this.swiperList.length < 5) {
        return;
      }
      this.swiperHeight=document.getElementsByClassName('otherclass')[0].clientHeight;
      var swiperfz = document.getElementById("swiperfz");
      swiperfz.style.top = -(this.activeSwiper - 2) * this.swiperHeight + "px";
      var childNodes = swiperfz.childNodes;
      childNodes.forEach((item) => {
        item.style.transitionDuration = this.duration;
      });
    },
    play() {
      var swiperfz = document.getElementById("swiperfz");
      this.timer = setInterval(() => {
        this.activeSwiper++;
        if (this.activeSwiper > this.swiperList.length - 5) {
          swiperfz.style.transition = "top 1s ease 0s";
          this.duration = "1s";
          swiperfz.style.top = -(this.activeSwiper - 2) * this.swiperHeight + "px";
          this.init();
          var changeIndex3 = () => {
            swiperfz.style.transition = "none";
            this.duration = "0s";
            this.activeSwiper = 4;
            swiperfz.style.top = -(this.activeSwiper - 2) * this.swiperHeight + "px";
            this.init();
            swiperfz.removeEventListener("transitionend", changeIndex3);
          };
          swiperfz.addEventListener("transitionend", changeIndex3);
        } else {
          swiperfz.style.top = -(this.activeSwiper - 2) * this.swiperHeight + "px";
          swiperfz.style.transition = "top 1s ease 0s";
          this.duration = "1s";
          this.init();
        }
      }, 2000);
    },
    change(index) {
      var swiperfz = document.getElementById("swiperfz");
      clearInterval(this.timer);
      if (index > this.swiperList.length - 5) {
        swiperfz.style.transition = "top 1s ease 0s";
        this.duration = "1s";
        swiperfz.style.top = -(index - 2) * this.swiperHeight + "px";
        this.activeSwiper = index;
        this.init();
        var changeIndex1 = () => {
          swiperfz.style.transition = "none";
          this.duration = "0s";
          swiperfz.style.top =
            -(index + 6 - this.swiperList.length) * this.swiperHeight + "px";
          this.activeSwiper = index + 8 - this.swiperList.length;
          this.init();
          swiperfz.removeEventListener("transitionend", changeIndex1);
        };
        swiperfz.addEventListener("transitionend", changeIndex1);
      } else if (index < 4) {
        swiperfz.style.transition = "top 1s ease 0s";
        this.duration = "1s";
        swiperfz.style.top = -(index - 2) * this.swiperHeight + "px";
        this.activeSwiper = index;
        this.init();
        var changeIndex2 = () => {
          swiperfz.style.transition = "none";
          this.duration = "0s";
          swiperfz.style.top =
            -(this.swiperList.length + index - 6) * this.swiperHeight + "px";
          this.activeSwiper = this.swiperList.length + index - 8;
          this.init();
          swiperfz.removeEventListener("transitionend", changeIndex2);
        };
        swiperfz.addEventListener("transitionend", changeIndex2);
      } else {
        swiperfz.style.top = -(index - 2) * this.swiperHeight + "px";
        swiperfz.style.transition = "top 1s ease 0s";
        this.duration = "1s";
        this.activeSwiper = index;
        this.init();
      }
    },
    mouseEnter(index) {
      clearInterval(this.timer);
      clearTimeout(this.timer2);
      if (index > this.swiperList.length - 3 || index < 2) {
        return;
      }
      this.timer3 = setTimeout(() => {
        this.change(index);
      }, 1000);
    },
    mouseLeave() {
      clearTimeout(this.timer3);
      this.timer3 = null;
      if (this.timer2) {
        clearTimeout(this.timer2);
        this.timer2 = null;
      }
      this.timer2 = setTimeout(() => {
        this.play();
      }, 2000);
    },
  },
};
</script>
<style lang="scss" scoped>
.twolunbo {
  width: 100%;
  min-width: $minWidth;
  height: 1080px;
  background: linear-gradient(135deg, #33405a 0%, #263045 100%);
  position: relative;
  @media screen and (max-width: $screenMaxWidth){
    min-width: $phoneWidth;
    height: 380px;
  }
  .bgImg2 {
    position: absolute;
    width: 905px;
    height: 905px;
    left: 50%;
    top: 50%;
    transform: translate(-50%, -50%);
    @media screen and (max-width: $screenMaxWidth){
      width: calc(100% - 80px );
      height: auto;
    }
  }
  .swiper-fz-content {
    height: 700px;
    width: 100%;
    top: 50%;
    transform: translate(0, -50%);
    overflow: hidden;
    position: absolute;
    @media screen and (max-width: $screenMaxWidth){
      height: 320px;
      // background-color: navajowhite;
    }
    #swiperfz{
      top: -168px;
      @media screen and (max-width: $screenMaxWidth){
        top: -80px;
      }
    }
    .swiper-fz {
      width: $headerMinWidth;
      left: 50%;
      position: absolute;
      transform: translate(-50%, 0);
      @media screen and (max-width: $screenMaxWidth){
        width: $phoneContentWidth;
      }
      .swiper-fz-item {
        position: relative;
        margin: 0 auto;
        background-color: rgb(116, 127, 151);
        text-align: center;
        cursor: pointer;
        .content-fz {
          position: absolute;
          top: 50%;
          left: 0;
          transform: translate(0, -50%);
          width: 100%;
          font-size: 28px;
          font-weight: 500;
          color: #ffffff;
          letter-spacing: 4px;
          text-shadow: 0px 1px 1px rgba(0, 0, 0, 0.3);
          @media screen and (max-width: $screenMaxWidth){
            padding: 0 30px;
            box-sizing: border-box;
            font-size: 18px;
          }
        }
      }
      .activeClass {
        width: $headerMinWidth;
        position: relative;
        height: 325px;
        opacity: 1;
        @media screen and (max-width: $screenMaxWidth){
          width: 100%;
          height: 120px;
        }
        &::before {
          content: "";
          width: 20px;
          height: 70%;
          position: absolute;
          background: linear-gradient((90deg, #ac2024 0%, #ef5d5d 100%));
          left: 0;
          top: 50%;
          transform: translate(0, -50%);
          @media screen and (max-width: $screenMaxWidth){
            width: 10px;
            height: 70%;
          }
        }
      }
      .twoClass{
        width: 1225px;
        height: 103px;
        opacity: 0.55;
        @media screen and (max-width: $screenMaxWidth){
          width: calc(100% - 30px );
          height: 60px;
        }
      }
      .otherclass{
        width: 1150px;
        height: 84px;
        opacity: 0.33;
        @media screen and (max-width: $screenMaxWidth){
          width: calc(100% - 60px );
          height: 40px;
        }
      }
    }
  }
}
</style>
