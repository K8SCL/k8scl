<template>
  <div
    class="body-bg"
    :style="`background: url(${bgImg}) center center / cover no-repeat;opacity:${opacity};`"
  ></div>
</template>

<script>
import storage from "good-storage";
import EventBus from "eventing-bus";
let timer = null;
export default {
  data() {
    return {
      bgImg: "",
      opacity: 0.5,
    };
  },
  created() {
    this.setBodyBgImage();
  },

  beforeMount() {},

  mounted() {
    EventBus.on("modeChange", () => {
      this.setBodyBgImage();
    });
  },
  beforeDestroy() {
    EventBus.unregisterCallbacksForEvent("modeChange");
  },
  methods: {
    switchImg(e) {
      if (e) {
        this.setBodyBgImage();
      } else {
        this.bgImg = "";
        clearInterval(timer);
      }
    },
    forceUpdate() {
      this.setBodyBgImage();
      this.$forceUpdate();
    },
    setBodyBgImage() {
      clearInterval(timer);
      let {
        bodyBgImg,
        bodyBgImgOpacity = 0.5,
        bodyBgImgInterval = 15,
      } = this.$themeConfig;
      const mode = storage.get("cur_mode");
      bodyBgImg = bodyBgImg.filter((s) => s.mode === mode);
      if (bodyBgImg.length === 0) {
        this.bgImg = "";
        clearInterval(timer);
        return;
      }
      let count = 0;
      this.bgImg = bodyBgImg[count].url;
      timer = setInterval(() => {
        if (++count >= bodyBgImg.length) {
          count = 0;
        }
        this.bgImg = bodyBgImg[count].url;
        // 预加载下一张图片
        if (bodyBgImg[count + 1]) {
          const img = new Image();
          img.src = bodyBgImg[count + 1].url;
        }
        if (bodyBgImg[count].opacity) {
          this.opacity = bodyBgImg[count].opacity;
        } else if (bodyBgImgOpacity !== undefined) {
          this.opacity = bodyBgImgOpacity;
        }
      }, bodyBgImgInterval * 1000);
      if (bodyBgImg[count].opacity) {
        this.opacity = bodyBgImg[count].opacity;
      } else if (bodyBgImgOpacity !== undefined) {
        this.opacity = bodyBgImgOpacity;
      }
    },
  },
};
</script>

<style lang='stylus'>
.body-bg {
  position: fixed;
  left: 0;
  top: 0;
  z-index: -999999;
  height: 100vh;
  width: 100vw;
  transition: background 2s;
}
</style>
