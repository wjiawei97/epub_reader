<template>
  <div class="ebook">
    <title-bar :isTitleAndMenuShow='isTitleAndMenuShow'></title-bar>
    <div class="read-wrapper">
      <div id="read"></div>
      <div class="mask">
        <div class="left"
             @click='prevPage'></div>
        <div class="center"
             @click="toggleTitleAndMenu"></div>
        <div class="right"
             @click='nextPage'></div>
      </div>
    </div>
    <menu-bar :isTitleAndMenuShow='isTitleAndMenuShow'
              :fontSizeList='fontSizeList'
              :defaultFontSize="defaultFontSize"
              @setFontSize='setFontSize'
              :themeList='themeList'
              :defaultTheme='defaultTheme'
              @setTheme='setTheme'
              @onProgressChange='onProgressChange'
              :bookAvailable='bookAvailable'
              @jumpTo='jumpTo'
              :navigation='navigation'
              ref="menuBar"></menu-bar>
  </div>
</template>

<script>
import TitleBar from '@/components/TitleBar'
import MenuBar from '@/components/MenuBar'

import ePub from 'epubjs'
const DOWNLOAD_URL = '../static/2018_Book_AgileProcessesInSoftwareEngine.epub'
export default {
  data () {
    return {
      // 是否显示标题栏和菜单栏
      isTitleAndMenuShow: false,
      // 字号列表
      fontSizeList: [
        { fontSize: 12 },
        { fontSize: 14 },
        { fontSize: 16 },
        { fontSize: 18 },
        { fontSize: 20 },
        { fontSize: 22 },
        { fontSize: 24 },
      ],
      // 默认字号
      defaultFontSize: 16,
      // 主题列表
      themeList: [
        {
          name: 'default',
          style: {
            body: {
              'color': '#000',
              'background': '#fff'
            }
          }
        },
        {
          name: 'eye',
          style: {
            body: {
              'color': '#000',
              'background': '#ceeaba'
            }
          }
        },
        {
          name: 'light', style: {
            body: {
              'color': '#fff',
              'background': '#000'
            }
          }
        },
        {
          name: 'gold', style: {
            body: {
              'color': '#000',
              'background': 'rgba(241,236,226)'
            }
          }
        }
      ],
      // 默认样式索引
      defaultTheme: 0,
      // 图书是否处于可用状态
      bookAvailable: false,
      // navigation对象
      navigation: null
    }
  },
  components: {
    TitleBar,
    MenuBar
  },
  methods: {
    // 根据链接跳转到指定位置，并隐藏标题栏、菜单栏、设置栏、目录
    jumpTo (href) {
      this.rendition.display(href)
      this.hideTitleAndMenu()
    },
    hideTitleAndMenu () {
      // 隐藏标题栏和菜单栏
      this.isTitleAndMenuShow = false
      // 隐藏菜单栏的设置栏
      this.$refs.menuBar.hideSetting()
      // 隐藏目录
      this.$refs.menuBar.hideContent()
    },
    // 根据进度条长度/100 设置当前位置
    onProgressChange (progress) {
      const percentage = progress / 100
      const location = percentage > 0 ? this.locations.cfiFromPercentage(percentage) : 0 // 将百分比转为位置
      this.rendition.display(location)
    },
    // 根据索引设置主题颜色
    setTheme (index) {
      this.themes.select(this.themeList[index].name)
      this.defaultTheme = index
    },
    // 注册主题
    registerTheme () {
      this.themeList.forEach((theme) => {
        // console.log(theme.name);
        this.themes.register(theme.name, theme.style)
      })
    },
    // 设置字号
    setFontSize (fontSize) {
      this.defaultFontSize = fontSize
      if (this.themes) {
        this.themes.fontSize(this.defaultFontSize + 'px')
      }
    },
    // 切换显示状态
    toggleTitleAndMenu () {
      this.isTitleAndMenuShow = !this.isTitleAndMenuShow
      // 如果需要隐藏menubar，则将menubar中的setting也隐藏
      if (!this.isTitleAndMenuShow) {
        this.$refs.menuBar.hideSetting()
      }
    },
    // 向左翻页
    prevPage () {
      if (this.rendition) {
        this.rendition.prev()
      }
    },
    // 向右翻页
    nextPage () {
      if (this.rendition) {
        this.rendition.next()
      }
    },
    // 电子书的解析和渲染
    showEpub () {
      // 解析为Book对象
      this.book = new ePub(DOWNLOAD_URL)
      // 通过renderTo实例化Rendition对象
      this.rendition = this.book.renderTo('read', {
        width: window.innerWidth,
        height: innerHeight
      })
      this.rendition.display()
      // 获取Theme对象
      this.themes = this.rendition.themes
      // 设置默认字号
      this.setFontSize(this.defaultFontSize)
      // 注册主题样式
      this.registerTheme()
      // 切换主题
      this.setTheme(this.defaultTheme)
      // 通过epubjs钩子函数实现获取locations对象
      this.book.ready.then(() => {
        // 获取navigation对象
        this.navigation = this.book.navigation
        // console.log(this.navigation);
        return this.book.locations.generate()
      }).then(result => {
        this.locations = this.book.locations
        this.bookAvailable = true
        // this.onProgressChange(10)
      })

    }
  },
  mounted () {
    this.showEpub()
  }
}
</script>

<style lang='scss' scoped>
@import "./assets/styles/global";
.ebook {
  position: relative;
  .read-wrapper {
    .mask {
      position: absolute;
      top: 0;
      left: 0;
      z-index: 100;
      display: flex;
      width: 100%;
      height: 100%;
      .left {
        flex: 0 0 px2rem(100);
        width: 50px;
      }

      .center {
        flex: 1;
      }
      .right {
        flex: 0 0 px2rem(100);
      }
    }
  }
}
</style>
