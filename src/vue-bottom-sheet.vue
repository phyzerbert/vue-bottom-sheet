<template>
  <div
      :class="[
      'bottom-sheet',
      {
        opened: opened,
        closed: opened === false,
        moving: moving
      }
    ]"
      ref="bottomSheet"
  >
    <div
        :style="[{ bottom: cardP+'px', maxWidth: maxWidth, maxHeight: maxHeight },{'height':isFullScreen ? '100%' : 'auto'},{'pointer-events': 'all'},{'background': backgroundColor}]"
        :class="[
        'bottom-sheet__card',
        { stripe: stripe, square: !rounded },
        effect
      ]"
        ref="bottomSheetCard"
    >
      <div class="bottom-sheet__pan" ref="pan" @click="opened ? close() : open()">
        <slot name="handler">
          <div class="bottom-sheet__bar"></div>
        </slot>
      </div>
      <div
          :style="{ height: contentH }"
          ref="bottomSheetCardContent"
          class="bottom-sheet__content"
      >
        <slot/>
      </div>
    </div>
  </div>
</template>

<script>
import Hammer from "hammerjs";

export default {
  name: "VueBottomSheet",
  data() {
    const vm = this;
    return {
      inited: false,
      opened: false,
      contentH: "auto",
      hammer: {
        pan: null,
        content: null,
      },
      contentScroll: 0,
      cardP: null,
      cardH: null,
      moving: false,
      stripe: 0,
      eventType: null,
    };
  },
  props: {
    maxWidth: {
      type: String,
      default: "640px"
    },
    maxHeight: {
      type: String,
      default: "90%"
    },
    minHeight: {
      type: Number,
      default: 40,
    },
    effect: {
      type: String,
      default: "fx-default"
    },
    rounded: {
      type: Boolean,
      default: true
    },
    swipeAble: {
      type: Boolean,
      default: true
    },
    isFullScreen: {
      type: Boolean,
      default: false
    },
    backgroundColor: {
      type: String,
      default: "#E5D7D7"
    },
    backgroundScrollable: {
      type: Boolean,
      default: false
    },
    swipeLimit: {
      type: Number,
      default: 50
    }
  },
  mounted() {
    this.init()
  },
  methods: {
    isIphone() {
      let iPhone = /iPhone/.test(navigator.userAgent) && !window.MSStream;
      let aspect = window.screen.width / window.screen.height;
      return iPhone && aspect.toFixed(3) === "0.462";
    },
    move(event, type) {
      if (this.swipeAble) {
        let delta = -event.deltaY;
        if (
            (type === 'content' && event.type === 'panup') ||
            (type === 'content' && event.type === 'pandown' && this.contentScroll > 0)
        ) {
          this.$refs.bottomSheetCardContent.scrollTop = this.contentScroll + delta;
        } else if (event.type === 'panup' || event.type === 'pandown') {
          this.moving = true;
          if (event.deltaY > 0) {
            this.cardP = delta;
          } else {
            this.cardP = 35 - this.cardH + delta;
          }
        }
        if (event.isFinal) {
          this.contentScroll = this.$refs.bottomSheetCardContent.scrollTop;
          this.moving = false;
          if ((this.cardP < -1 * this.swipeLimit && this.eventType === 'pandown') || (this.cardP < this.swipeLimit - this.cardH && this.eventType === 'panup')) {
            this.cardP = -this.cardH - this.stripe + this.minHeight;
            document.body.style.overflow = "";
            this.opened = false;
            this.$emit("closed");
          } else {
            this.cardP = 0;
            this.opened = true
            this.$emit("opened");
          }
          // this.eventType = null;
        } else {
          this.eventType = event.type
        }
      }
    },
    init() {
      return new Promise(resolve => {
        this.contentH = "auto";
        this.stripe = this.isIphone() ? 20 : 0;
        this.cardH = this.$refs.bottomSheetCard.clientHeight;
        this.contentH = `${this.cardH - this.$refs.pan.clientHeight}px`;
        this.$refs.bottomSheetCard.style.maxHeight = this.maxHeight;
        this.cardP =
            this.effect === "fx-slide-from-right" ||
            this.effect === "fx-slide-from-left"
                ? 0
                : -this.cardH - this.stripe + this.minHeight;
        if (!this.inited) {
          this.inited = true;
          let options = {
            recognizers: [
              [Hammer.Pan, {direction: Hammer.DIRECTION_VERTICAL}]
            ]
          }
          this.hammer.pan = new Hammer(this.$refs.pan, options);
          this.hammer.pan.on("panstart panup pandown panend", e => {
            this.move(e, 'pan')
          })
          this.hammer.content = new Hammer(this.$refs.bottomSheetCardContent, options);
          this.hammer.content.on("panstart panup pandown panend", e => {
            this.move(e, 'content')
          })
        }
        setTimeout(() => {
          resolve();
        }, 10);
      });
    },
    open() {
      this.init().then(() => {
        this.opened = true;
        this.cardP = 0;

        if (!this.$props.backgroundScrollable) {
          document.body.style.overflow = "hidden";
        }

        this.$emit("opened");
      });
    },
    close() {
      this.opened = false;
      this.cardP =
          this.effect === "fx-slide-from-right" ||
          this.effect === "fx-slide-from-left"
              ? 0
              : -this.cardH - this.stripe + this.minHeight;
      document.body.style.overflow = "";
      this.$emit("closed");
    },
  },
  beforeDestroy() {
    this.hammer?.pan?.destroy();
    this.hammer?.content?.destroy();
  }
};
</script>

<style lang="scss" scoped>
.bottom-sheet {
  z-index: 99999;
  transition: all 0.4s ease;
  position: relative;
  pointer-events: all;

  * {
    box-sizing: border-box;
  }

  &__content {
    overflow-y: scroll;
  }

  &__backdrop {
    position: fixed;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    z-index: 9999;
    opacity: 0;
    visibility: hidden;
  }

  &__card {
    width: 100%;
    position: fixed;
    border-radius: 8px 8px 0 0;
    left: 50%;
    z-index: 9999;
    margin: 0 auto;

    &.square {
      border-radius: 0;
    }

    &.stripe {
      padding-bottom: 20px;
    }

    &.fx-default {
      transform: translate(-50%, 0);
      transition: bottom 0.3s ease;
    }

    &.fx-fadein-scale {
      transform: translate(-50%, 0) scale(0.7);
      opacity: 0;
      transition: all 0.3s;
    }

    &.fx-slide-from-right {
      transform: translate(100%, 0);
      opacity: 0;
      transition: all 0.3s cubic-bezier(0.25, 0.5, 0.5, 0.9);
    }

    &.fx-slide-from-left {
      transform: translate(-100%, 0);
      opacity: 0;
      transition: all 0.3s cubic-bezier(0.25, 0.5, 0.5, 0.9);
    }
  }

  &__pan {
    padding-bottom: 8px;
    padding-top: 8px;
    text-align: center;
  }

  &__bar {
    display: block;
    width: 40px;
    height: 4px;
    border-radius: 14px;
    margin: 0 auto;
    cursor: pointer;
    background: rgba(0, 0, 0, 0.3);
  }

  &.moving {
    .bottom-sheet__card {
      transition: none;
    }
  }

  &.opened {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;

    .bottom-sheet__backdrop {
      animation: show 0.3s ease;
      opacity: 1;
      visibility: visible;
    }

    .bottom-sheet__card {
      &.fx-fadein-scale {
        transform: translate(-50%, 0) scale(1);
        opacity: 1;
      }

      &.fx-slide-from-right {
        transform: translate(-50%, 0);
        opacity: 1;
      }

      &.fx-slide-from-left {
        transform: translate(-50%, 0);
        opacity: 1;
      }
    }
  }
}

@keyframes show {
  0% {
    opacity: 0;
    visibility: hidden;
  }

  100% {
    opacity: 1;
    visibility: visible;
  }
}

@keyframes hide {
  0% {
    opacity: 1;
    visibility: visible;
  }

  100% {
    opacity: 0;
    visibility: hidden;
  }
}
</style>
