<template>
  <div class="ebook">
    <!-- 标题栏 -->
    <title-bar :ifTitleAndMenu="ifTitleAndMenu"></title-bar>
    <div class="read-wrapper">
      <div id="read"></div>
      <div class="mask">
        <div @click="prevPage" class="left"></div>
        <div @click="toggleTitleAndMenu" class="center"></div>
        <div @click="nextPage" class="right"></div>
      </div>
    </div>
    <!-- 菜单栏 -->
    <menu-bar
      :bookAvailable="bookAvailable"
      :defaultFontSize="defaultFontSize"
      :defaultTheme="defaultTheme"
      :fontSizeList="fontSizeList"
      :ifTitleAndMenu="ifTitleAndMenu"
      :navigation="navigation"
      :themeList="themeList"
      @jumpTo="jumpTo"
      @onProgressChange="onProgressChange"
      @setFontSize="setFontSize"
      @setTheme="setTheme"
      ref="menuBar"
    ></menu-bar>
  </div>
</template>
<script>
import Epub from 'epubjs'
import TitleBar from './components/TitleBar'
import MenuBar from './components/MenuBar'
const DOWNLOAD_URL = '/static/2018_Book_AgileProcessesInSoftwareEngine.epub'
// global.epub = Epub
export default {
  data () {
    return {
      ifTitleAndMenu: false,
      defaultFontSize: 16,
      fontSizeList: [
        { fontSize: 12 },
        { fontSize: 14 },
        { fontSize: 16 },
        { fontSize: 18 },
        { fontSize: 20 },
        { fontSize: 22 },
        { fontSize: 24 }
      ],
      defaultTheme: 0,
      themeList: [
        {
          name: 'default',
          style: {
            body: {
              'color': '#000', 'background': '#fff'
            }
          }
        },
        {
          name: 'eye',
          style: {
            body: {
              'color': '#000', 'background': '#ceeaba'
            }
          }
        },
        {
          name: 'night',
          style: {
            body: {
              'color': '#fff', 'background': '#000'
            }
          }
        },
        {
          name: 'gold',
          style: {
            body: {
              'color': '#000', 'background': 'rgb(241,236,226)'
            }
          }
        }
      ],
      bookAvailable: false,
      navigation: {}
    }
  },
  methods: {
    // 电子书的解析和渲染
    showEpub () {
      // 生成book、hideSetting
      this.book = new Epub(DOWNLOAD_URL)
      console.log('this.book', this.book)
      // 生成rendtion对象
      this.rendition = this.book.renderTo('read', {
        width: window.innerWidth,
        height: window.innerHeight
      })
      // 通过dispaly展示
      this.rendition.display()
      this.themes = this.rendition.themes
      // 设置默认字体
      this.setFontSize(this.defaultFontSize)

      // this.themes.register(name, styles)
      this.registerTheme()
      // this.themes.select('eye')
      this.setTheme(this.defaultTheme) // 设置主题
      // 获取进度条location对象，默认不会生成
      // 通过epubjs的钩子函数来实现
      // this.book.locations()
      this.book.ready.then(() => {
        this.navigation = this.book.navigation
        return this.book.locations.generate()
      }).then(res => {
        this.locations = this.book.locations
        // this.onProgressChange()
        this.bookAvailable = true
      })
    },
    toggleTitleAndMenu () {
      this.ifTitleAndMenu = !this.ifTitleAndMenu
      if (!this.ifTitleAndMenu) {
        this.$refs.menuBar.hideSetting()
      }
    },
    // 上一页
    prevPage () {
      if (this.rendition) {
        this.rendition.prev()
      }
    },
    // 下一页
    nextPage () {
      if (this.rendition) {
        this.rendition.next()
      }
    },
    setFontSize (fontSize) {
      this.defaultFontSize = fontSize
      if (this.themes) {
        this.themes.fontSize(fontSize + 'px')
      }
    },
    setTheme (index) {
      this.themes.select(this.themeList[index].name)
      this.defaultTheme = index
    },
    registerTheme () {
      this.themeList.forEach(theme => {
        this.themes.register(theme.name, theme.style)
      })
    },
    onProgressChange (progress) {
      const percentage = progress / 100
      const location = percentage > 0 ? this.locations.cfiFromPercentage(percentage) : 0
      this.rendition.display(location)
    },
    hideTitleAndMenu () {
      this.ifTitleAndMenu = false
      this.$refs.menuBar.hideSetting()
      this.$refs.menuBar.hideContent()
    },
    // 根据链接跳转
    jumpTo (href) {
      this.rendition.display(href)
      this.hideTitleAndMenu()
    }

  },
  components: {
    MenuBar,
    TitleBar
  },
  mounted () {
    this.showEpub()
  }
}
</script>
<style lang="scss" scoped>
@import './assets/styles/global';
.ebook {
  position: relative;

  .read-wrapper {
    .mask {
      position: absolute;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      z-index: 100;
      display: flex;
      .left {
        flex: 0 0 px2rem(100);
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
