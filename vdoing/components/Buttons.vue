<template>
  <div class="buttons">
    <transition name="fade">
      <div
        title="返回顶部"
        class="button blur go-to-top iconfont icon-fanhuidingbu"
        v-show="showToTop"
        @click="scrollToTop"
      />
    </transition>
    <div
      title="去评论"
      class="button blur go-to-comment iconfont icon-pinglun"
      v-show="showCommentBut"
      @click="scrollToComment"
    />
    <!-- <div
      title="设置"
      class="button blur setting iconfont icon-setting"
      @mouseenter="showSettingBox = true"
      @mouseleave="showSettingBox = false"
      @click="showSettingBox = true"
    >
      <transition name="mode">
        <ul
          class="select-box"
          ref="modeBox"
          v-show="showSettingBox"
          @click.stop
          @touchstart.stop
        >
          <li>
            <div style="display: flex; padding: 5px; justify-content: center">
              <span>背景图片</span>
              <input
                ref="bgSwatch"
                class="switch switch-anim"
                @change="bgSwitchChange"
                type="checkbox"
                checked
              />
            </div>
          </li>
        </ul>
      </transition>
    </div> -->
    <div
      title="主题模式"
      class="button blur theme-mode-but iconfont icon-zhuti"
      @mouseenter="showModeBox = true"
      @mouseleave="showModeBox = false"
      @click="showModeBox = true"
    >
      <transition name="mode">
        <ul
          class="select-box"
          ref="modeBox"
          v-show="showModeBox"
          @click.stop
          @touchstart.stop
        >
          <li
            v-for="item in modeList"
            :key="item.KEY"
            class="iconfont"
            :class="[item.icon, { active: item.KEY === currentMode }]"
            @click="toggleMode(item.KEY)"
          >
            {{ item.name }}
          </li>
        </ul>
      </transition>
    </div>
  </div>
</template>

<script>
import debounce from "lodash.debounce";
import storage from "good-storage"; // 本地存储
const MOBILE_DESKTOP_BREAKPOINT = 719; // refer to config.styl

export default {
  data() {
    return {
      threshold: 100,
      scrollTop: null,
      showCommentBut: false,
      commentTop: null,
      currentMode: "",
      showModeBox: false,
      showSettingBox: false,
      modeList: [
        {
          name: "跟随系统",
          icon: "icon-zidong",
          KEY: "auto",
        },
        {
          name: "浅色模式",
          icon: "icon-rijianmoshi",
          KEY: "light",
        },
        {
          name: "深色模式",
          icon: "icon-yejianmoshi",
          KEY: "dark",
        },
        {
          name: "阅读模式",
          icon: "icon-yuedu",
          KEY: "read",
        },
      ],
      _scrollTimer: null,
      _textareaEl: null,
      _recordScrollTop: null,
      COMMENT_SELECTOR_1: "#vuepress-plugin-comment", // 评论区元素的选择器1
      COMMENT_SELECTOR_2: "#valine-vuepress-comment", // 评论区元素的选择器2
      COMMENT_SELECTOR_3: ".vssue", // 评论区元素的选择器3
    };
  },
  mounted() {
    this.currentMode =
      storage.get("mode") || this.$themeConfig.defaultMode || "auto";
    this.scrollTop = this.getScrollTop();
    window.addEventListener(
      "scroll",
      debounce(() => {
        this.scrollTop = this.getScrollTop();
      }, 100)
    );

    window.addEventListener("load", () => {
      this.getCommentTop();
    });

    // 小屏时选择主题模式后关闭选择框
    if (document.documentElement.clientWidth < MOBILE_DESKTOP_BREAKPOINT) {
      const modeBox = this.$refs.modeBox;
      modeBox.onclick = () => {
        this.showModeBox = false;
      };
      window.addEventListener(
        "scroll",
        debounce(() => {
          if (this.showModeBox) {
            this.showModeBox = false;
          }
        }, 100)
      );
    }

    // 移动端对类似:hover效果的处理
    const buttons = document.querySelectorAll(".buttons .button");
    for (let i = 0; i < buttons.length; i++) {
      const button = buttons[i];
      button.addEventListener("touchstart", function () {
        button.classList.add("hover");
      });
      button.addEventListener("touchend", function () {
        setTimeout(() => {
          button.classList.remove("hover");
        }, 150);
      });
    }
  },
  computed: {
    showToTop() {
      return this.scrollTop > this.threshold;
    },
  },
  methods: {
    bgSwitchChange() {
      this.$emit("changeBodyBgImg", this.$refs.bgSwatch.checked);
    },

    toggleMode(key) {
      this.currentMode = key;
      this.$emit("toggle-theme-mode", key);
    },
    getScrollTop() {
      return (
        window.pageYOffset ||
        document.documentElement.scrollTop ||
        document.body.scrollTop ||
        0
      );
    },

    scrollToTop() {
      window.scrollTo({ top: 0, behavior: "smooth" });
      this.scrollTop = 0;
    },

    getCommentTop() {
      setTimeout(() => {
        let commentEl =
          document.querySelector(this.COMMENT_SELECTOR_1) ||
          document.querySelector(this.COMMENT_SELECTOR_2) ||
          document.querySelector(this.COMMENT_SELECTOR_3);
        if (commentEl) {
          this.showCommentBut =
            this.$frontmatter.comment !== false &&
            this.$frontmatter.home !== true;
          this.commentTop = commentEl.offsetTop - 58;
        }
      }, 500);
    },

    scrollToComment() {
      window.scrollTo({ top: this.commentTop, behavior: "smooth" });
      this._textareaEl =
        document.querySelector(this.COMMENT_SELECTOR_1 + " textarea") ||
        document.querySelector(this.COMMENT_SELECTOR_2 + " input") ||
        document.querySelector(this.COMMENT_SELECTOR_3 + " textarea");
      if (this._textareaEl && this.getScrollTop() !== this._recordScrollTop) {
        document.addEventListener("scroll", this._handleListener);
      } else if (
        this._textareaEl &&
        this.getScrollTop() === this._recordScrollTop
      ) {
        this._handleFocus();
      }
    },

    _handleListener() {
      clearTimeout(this._scrollTimer);
      this._scrollTimer = setTimeout(() => {
        document.removeEventListener("scroll", this._handleListener);
        this._recordScrollTop = this.getScrollTop();
        this._handleFocus();
      }, 30);
    },

    _handleFocus() {
      this._textareaEl.focus();
      this._textareaEl.classList.add("yellowBorder");
      setTimeout(() => {
        this._textareaEl.classList.remove("yellowBorder");
      }, 500);
    },
  },
  watch: {
    "$route.path"() {
      this.showCommentBut = false;
      this.getCommentTop();
    },
  },
};
</script>

<style lang='stylus'>
.yellowBorder {
  // border: #FFE089 1px solid!important
  border-radius: 5px;
  box-shadow: 0 0 15px #FFE089 !important;
}

.buttons {
  position: fixed;
  right: 2rem;
  bottom: 2.5rem;
  z-index: 11;

  @media (max-width: $MQNarrow) {
    right: 1rem;
    bottom: 1.5rem;
  }

  .button {
    width: 2.2rem;
    height: 2.2rem;
    line-height: 2.2rem;
    border-radius: 50%;
    box-shadow: 0 2px 6px rgba(0, 0, 0, 0.15);
    margin-top: 0.9rem;
    text-align: center;
    cursor: pointer;
    transition: all 0.5s;
    background: var(--blurBg);

    &.hover {
      background: $accentColor;
      box-shadow: 0 0 15px $accentColor;

      &:before {
        color: #fff;
      }
    }

    @media (any-hover: hover) {
      &:hover {
        background: $accentColor;
        box-shadow: 0 0 15px $accentColor;

        &:before {
          color: #fff;
        }
      }
    }

    .select-box {
      margin: 0;
      padding: 0.8rem 0;
      position: absolute;
      bottom: 0rem;
      right: 1.5rem;
      background: var(--mainBg);
      border: 1px solid var(--borderColor);
      min-width: 120px;
      width: 150px;
      border-radius: 6px;
      box-shadow: 0 0 15px rgba(255, 255, 255, 0.2);

      li {
        list-style: none;
        line-height: 2rem;
        font-size: 0.95rem;

        &:hover {
          color: $accentColor;
        }

        &.active {
          background-color: rgba(150, 150, 150, 0.2);
          color: $accentColor;
        }
      }
    }
  }
}

.mode-enter-active, .mode-leave-active {
  transition: all 0.3s;
}

.mode-enter, .mode-leave-to {
  opacity: 0;
  transform: scale(0.8);
}

.fade-enter-active, .fade-leave-active {
  transition: opacity 0.2s;
}

.fade-enter, .fade-leave-to {
  opacity: 0;
}

.switch {
  width: 57px;
  height: 28px;
  position: relative;
  border: 1px solid #dfdfdf;
  background-color: #fdfdfd;
  box-shadow: #dfdfdf 0 0 0 0 inset;
  border-radius: 20px;
  background-clip: content-box;
  display: inline-block;
  -webkit-appearance: none;
  user-select: none;
  outline: none;
}

.switch:before {
  content: '';
  width: 26px;
  height: 26px;
  position: absolute;
  top: 0;
  left: 0;
  border-radius: 20px;
  background-color: #fff;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.4);
}

.switch:checked {
  border-color: #64bd63;
  box-shadow: #64bd63 0 0 0 16px inset;
  background-color: #64bd63;
}

.switch:checked:before {
  left: 30px;
}

.switch.switch-anim {
  transition: border cubic-bezier(0, 0, 0, 1) 0.4s, box-shadow cubic-bezier(0, 0, 0, 1) 0.4s;
}

.switch.switch-anim:before {
  transition: left 0.3s;
}

.switch.switch-anim:checked {
  box-shadow: #64bd63 0 0 0 16px inset;
  background-color: #64bd63;
  transition: border ease 0.4s, box-shadow ease 0.4s, background-color ease 1.2s;
}

.switch.switch-anim:checked:before {
  transition: left 0.3s;
}
</style>
