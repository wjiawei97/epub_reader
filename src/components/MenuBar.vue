<template>

  <div class="menu-bar">
    <transition name="slide-up">
      <div class="menu-wrapper"
           v-show="isTitleAndMenuShow"
           :class="{'hide-box-shadow':isSettingShow || !isTitleAndMenuShow}">
        <div class="icon-wrapper">
          <span class="icon-menu icon"
                @click="showSetting(3)"></span>
        </div>
        <div class="icon-wrapper">
          <span class="icon-progress icon"
                @click="showSetting(2)"></span>
        </div>
        <div class="icon-wrapper">
          <span class="icon-bright icon"
                @click="showSetting(1)"></span>
        </div>
        <div class="icon-wrapper">
          <span class="icon-a icon"
                @click="showSetting(0)">A</span>
        </div>
      </div>
    </transition>
    <transition name="slide-up">
      <div class="setting-wrapper"
           v-show="isSettingShow">
        <div class="setting-font-size"
             v-if="showTag===0">
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
        <div class="setting-theme"
             v-else-if="showTag===1">
          <div class="setting-theme-item"
               v-for="(item,index) in themeList"
               @click="setTheme(index)"
               :key="index">
            <div class="preview"
                 :style="{backgroundColor:item.style.body.background}"
                 :class="{'no-border':index!==0}"></div>
            <div class="text"
                 :class="{'text-selected':index===defaultTheme}">{{item.name}}</div>
          </div>
        </div>
        <div class="setting-progress"
             v-else-if="showTag===2">
          <div class="progress-wrapper">
            <input class="progress"
                   type="range"
                   max="100"
                   min="0"
                   step="1"
                   @change="onProgressChange($event.target.value)"
                   @input="onProgressInput($event.target.value)"
                   :value="progress"
                   :disabled='!bookAvailable'
                   ref="progress">
          </div>
          <div class="text-wrapper">
            <span>{{bookAvailable?progress+'%':'加载中...'}}</span>
          </div>
        </div>
      </div>
    </transition>

    <!-- 目录区域 -->
    <content-view :navigation='navigation'
                  :bookAvailable='bookAvailable'
                  @jumpTo='jumpTo'
                  v-show="ifShowContent"></content-view>
    <!-- 目录蒙版区域 -->
    <transition name="fade">
      <div class="content-mask"
           v-show="ifShowContent">

      </div>
    </transition>

  </div>

</template>

<script>
import ContentView from '@/components/Content'

export default {
  data () {
    return {
      isSettingShow: false,
      // 控制菜单栏子项栏的显示与隐藏
      showTag: 0,
      progress: 0,
      // 是否显示目录
      ifShowContent: false
    }
  },
  methods: {
    // 拖动进度条时触发事件
    onProgressInput (value) {
      this.progress = value
      this.$refs.progress.style.backgroundSize = `${this.progress}% 100%`
    },
    // 进度条松开后触发事件，根据进度条数值跳转到指定位置
    onProgressChange (value) {
      this.$emit('onProgressChange', value)
    },
    // 根据索引设置主题颜色
    setTheme (index) {
      this.$emit('setTheme', index)
    },
    // 点击进度条，设置字号
    setFontSize (fontSize) {
      this.$emit('setFontSize', fontSize)
    },
    // 控制字号设置窗口的显示
    showSetting (index) {
      if (index === 3) {
        // 如果是目录
        this.ifShowContent = true
      } else {
        this.isSettingShow = true
      }

      this.showTag = index
    },
    // 控制设置窗口的隐藏
    hideSetting () {
      this.isSettingShow = false
    },
    hideContent () {
      this.ifShowContent = false
    },
    jumpTo (href) {
      this.$emit('jumpTo', href)
    }
  },
  props: {
    isTitleAndMenuShow: {
      type: Boolean,
      default: false
    },
    fontSizeList: Array,
    defaultFontSize: Number,
    themeList: Array,
    defaultTheme: Number,
    bookAvailable: Boolean,
    navigation: Object
  },
  components: {
    ContentView
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
    .setting-theme {
      height: 100%;
      display: flex;
      .setting-theme-item {
        display: flex;
        flex-direction: column;
        flex: 1;
        padding: px2rem(5);
        box-sizing: border-box;
        .preview {
          flex: 1;
          border: px2rem(1) solid #ccc;
          box-sizing: border-box;
          &.no-border {
            border: none;
          }
        }
        .text {
          flex: 0 0 px2rem(20);
          font-size: px2rem(14);
          color: #ccc;
          @include center;
          &.text-selected {
            color: #333;
          }
        }
      }
    }
    .setting-progress {
      width: 100%;
      height: 100%;
      // position: relative;
      .progress-wrapper {
        width: 100%;
        height: 100%;
        @include center;
        padding: 0 px2rem(30);
        box-sizing: border-box;
        .progress {
          width: 100%;
          height: px2rem(2);
          appearance: none;
          background: linear-gradient(#999, #999) no-repeat, #ccc;
          background-size: 0 100%;
          &:focus {
            outline: none;
          }
          &::-webkit-slider-thumb {
            -webkit-appearance: none;
            width: px2rem(20);
            height: px2rem(20);
            border: px2rem(1) solid #ddd;
            border-radius: 50%;
            box-shadow: 0 4px 4px rgba(0, 0, 0, 0.15);
            background-color: #fff;
          }
        }
      }
      .text-wrapper {
        @include center;
        margin-top: -8px;
        span {
          color: #999;
          font-size: px2rem(12);
        }
      }
    }
  }
  .content-mask {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    z-index: 101;
    background: rgba(51, 51, 51, 0.8);
  }
}
</style>