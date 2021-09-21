<template>

  <div class="menu-bar">
    <transition name="slide-up">
      <div class="menu-wrapper"
           v-show="isTitleAndMenuShow"
           :class="{'hide-box-shadow':isSettingShow || !isTitleAndMenuShow}">
        <div class="icon-wrapper">
          <span class="icon-menu icon"></span>
        </div>
        <div class="icon-wrapper">
          <span class="icon-progress icon"></span>
        </div>
        <div class="icon-wrapper">
          <span class="icon-bright icon"></span>
        </div>
        <div class="icon-wrapper">
          <span class="icon-a icon"
                @click="showSetting">A</span>
        </div>
      </div>
    </transition>
    <transition name="slide-up">
      <div class="setting-wrapper"
           v-show="isSettingShow">
        <div class="setting-font-size">
          <div class="preview"
               :style="{fontSize:fontSizeList[0].fontSize+'px'}">A</div>
          <div class="select">
            <div class="select-wrapper"
                 v-for="(item,index) in fontSizeList"
                 :key="index"
                 @click="setFontSize(item.fontSize)">
              <div class="line"></div>
              <div class="point-wrapper">
                <div class="point"
                     v-show="defaultFontSize===item.fontSize">
                  <div class="small-point"></div>
                </div>
              </div>
            </div>
          </div>
          <div class="preview"
               :style="{fontSize:fontSizeList[fontSizeList.length-1].fontSize+'px'}">A</div>
        </div>
      </div>
    </transition>

  </div>

</template>

<script>
export default {
  data () {
    return {
      isSettingShow: false
    }
  },
  methods: {
    // 点击进度条，设置字号
    setFontSize (fontSize) {
      this.$emit('setFontSize', fontSize)
    },
    // 控制字号设置窗口的显示
    showSetting () {
      this.isSettingShow = true
    },
    // 控制字号设置窗口的隐藏
    hideSetting () {
      this.isSettingShow = false
    }
  },
  props: {
    isTitleAndMenuShow: {
      type: Boolean,
      default: false
    },
    fontSizeList: Array,
    defaultFontSize: Number
  }
}
</script>

<style lang='scss' scoped>
@import "../assets/styles/global";
.menu-bar {
  .menu-wrapper {
    position: absolute;
    bottom: 0;
    left: 0;
    z-index: 101;
    width: 100%;
    height: px2rem(48);
    display: flex;
    background-color: #fff;
    box-shadow: 0 px2rem(-8) px2rem(8) rgba(0, 0, 0, 0.15);
    &.hide-box-shadow {
      box-shadow: none;
    }
    .icon-wrapper {
      flex: 1;
      @include center;
      .icon-progress {
        font-size: px2rem(24);
      }
      .icon-bright {
        font-size: px2rem(24);
      }
    }
    // &.slide-up-enter,
    // &.slide-up-leave-to {
    //   transform: translate3d(0, 100%, 0);
    // }
    // &.slide-up-enter-to,
    // &.slide-up-leave {
    //   transform: translate3d(0, 0, 0);
    // }
    // &.slide-up-enter-active,
    // &.slide-up-leave-active {
    //   transition: all 0.3s linear;
    // }
  }
  .setting-wrapper {
    position: absolute;
    bottom: px2rem(48);
    left: 0;
    width: 100%;
    height: px2rem(60);
    background-color: #fff;
    z-index: 101;
    box-shadow: 0 px2rem(-8) px2rem(8) rgba(0, 0, 0, 0.15);
    .setting-font-size {
      height: 100%;
      display: flex;
      .preview {
        flex: 0 0 px2rem(40);
        @include center;
      }
      .select {
        display: flex;
        flex: 1;
        margin-left: -50px;
        .select-wrapper {
          flex: 1;
          display: flex;
          align-items: center;
          &:first-child {
            .line {
              &:first-child {
                border-top: none;
              }
            }
          }
          &:last-child {
            .line {
              &:last-child {
                display: none;
              }
            }
          }
          .line {
            flex: 1;
            height: 0;
            border-top: px2rem(2) solid #ccc;
          }
          .point-wrapper {
            position: relative;
            flex: 0 0 0;
            width: 0;
            height: px2rem(7);
            border-left: px2rem(2) solid #ccc;
            .point {
              position: absolute;
              top: px2rem(-8);
              left: px2rem(-10);
              border-radius: 50%;
              background-color: #fff;
              border: px2rem(1) solid #ccc;
              box-shadow: 0 px2rem(4) px2rem(4) rgba(0, 0, 0, 0.15);
              width: px2rem(20);
              height: px2rem(20);
              @include center;
              .small-point {
                width: px2rem(5);
                height: px2rem(5);
                background-color: #000;
                border-radius: 50%;
              }
            }
          }
        }
      }
    }
  }
}
</style>