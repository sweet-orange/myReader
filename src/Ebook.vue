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
      :defaultFontSize="defaultFontSize"
      :fontSizeList="fontSizeList"
      :ifTitleAndMenu="ifTitleAndMenu"
      @setFontSize="setFontSize"
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
      ]
    }
  },
  methods: {
    // 电子书的解析和渲染
    showEpub () {
      // 生成book、hideSetting
      this.book = new Epub(DOWNLOAD_URL)
      console.log(this.book)
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
