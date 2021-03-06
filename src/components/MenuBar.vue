<template>
  <div class="menu-bar">
    <transition name="slide-up">
      <div
        :class="{'hide-box-shadow':ifSettingShow || !ifTitleAndMenu }"
        class="menu-wrapper"
        v-show="ifTitleAndMenu"
      >
        <div class="icon-wrapper">
          <span @click="showSetting(3)" class="icon-menu icon"></span>
        </div>
        <div class="icon-wrapper">
          <span @click="showSetting(2)" class="icon-progress icon"></span>
        </div>
        <div class="icon-wrapper">
          <span @click="showSetting(1)" class="icon-bright icon"></span>
        </div>
        <div class="icon-wrapper">
          <span @click="showSetting(0)" class="icon-a icon">A</span>
        </div>
      </div>
    </transition>
    <transition name="slide-up">
      <div class="setting-wrapper" v-show="ifSettingShow">
        <!-- 设置字体大小 -->
        <div class="setting-font-size" v-if="showTag === 0">
          <div :style="{fontSize:fontSizeList[0].fontSize + 'px'}" class="preview">A</div>
          <div class="select">
            <div
              :key="index"
              @click="setFontSize(item.fontSize)"
              class="select-wrapper"
              v-for="(item,index) in fontSizeList"
            >
              <div class="line"></div>
              <div class="point-wrapper">
                <div class="point" v-show="defaultFontSize === item.fontSize">
                  <div class="small-point"></div>
                </div>
              </div>
              <div class="line"></div>
            </div>
          </div>
          <div
            :style="{fontSize:fontSizeList[fontSizeList.length-1].fontSize + 'px'}"
            class="preview"
          >A</div>
        </div>
        <!-- 设置主题 -->
        <div class="setting-theme" v-else-if="showTag === 1">
          <div
            :key="index"
            @click="setTheme(index)"
            class="setting-theme-item"
            v-for="(item,index) in themeList"
          >
            <div
              :class="{'no-border':item.style.body.background !== '#fff'}"
              :style="{background:item.style.body.background}"
              class="preview"
            ></div>
            <div :class="{'selected':index === defaultTheme}" class="text">{{item.name}}</div>
          </div>
        </div>
        <!-- 设置进度条 -->
        <div class="setting-progress" v-else-if="showTag === 2">
          <div class="progress-wrapper">
            <input
              :disabled="!bookAvailable"
              :value="progress"
              @change="onProgressChange($event.target.value)"
              @input="onProgressInput($event.target.value)"
              class="progress"
              max="100"
              min="0"
              ref="progress"
              step="1"
              type="range"
            />
          </div>
          <div class="text-wrapper">
            <span>{{bookAvailable ? progress + '%':'加载中...'}}</span>
          </div>
        </div>
      </div>
    </transition>
    <content-view
      :bookAvailable="bookAvailable"
      :ifShowContent="ifShowContent"
      :navigation="navigation"
      @jumpTo="jumpTo"
      v-show="ifShowContent"
    ></content-view>
    <transition name="fade">
      <div @click="hideContent" class="content-mask" v-show="ifShowContent"></div>
    </transition>
  </div>
</template>
<script>
import contentView from '@/components/Content'
export default {
  props: {
    ifTitleAndMenu: {
      type: Boolean,
      default: false
    },
    fontSizeList: {
      type: Array
    },
    defaultFontSize: Number,
    themeList: Array,
    defaultTheme: Number,
    bookAvailable: Boolean,
    navigation: Object
  },
  components: {
    contentView
  },
  data () {
    return {
      ifSettingShow: false,
      showTag: 0,
      progress: 0,
      ifShowContent: false
    }
  },
  methods: {
    showSetting (tag) {
      this.showTag = tag
      if (this.showTag === 3) {
        this.ifSettingShow = false
        this.ifShowContent = true
      } else {
        this.ifSettingShow = true
      }
    },
    hideSetting () {
      this.ifSettingShow = false
    },
    setFontSize (fontSize) {
      this.$emit('setFontSize', fontSize)
    },
    setTheme (index) {
      this.$emit('setTheme', index)
    },
    // 拖动进度条时触发事件
    onProgressInput (progress) {
      this.progress = progress
      this.$refs.progress.style.backgroundSize = `${this.progress}% 100%`
    },
    // 进度条松开后触发事件，根据进度条数值跳转到指定位置
    onProgressChange (progress) {
      this.$emit('onProgressChange', progress)
    },
    hideContent () {
      this.ifShowContent = false
    },
    jumpTo (target) {
      this.$emit('jumpTo', target)
    }
  }
}
</script>
<style lang="scss" scoped>
@import '../assets/styles/global';
.menu-bar {
  .slide-up-enter,
  .slide-up-leave-to {
    transform: translate3d(0, px2rem(108), 0);
  }
  .slide-up-enter-to,
  .slide-up-leave {
    transform: translate3d(0, 0, 0);
  }
  .slide-up-enter-active,
  .slide-up-leave-active {
    transition: all 0.3s linear;
  }
  .fade-enter,
  .fade-leave-to {
    opacity: 0;
  }
  .fade-enter-to,
  .fade-leave {
    opacity: 1;
  }
  .fade-enter-active,
  .fade-leave-active {
    transition: all 0.3s linear;
  }
}
.menu-wrapper {
  position: absolute;
  left: 0;
  bottom: 0;
  z-index: 101;
  width: 100%;
  height: px2rem(48);
  display: flex;
  background: #fff;
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
}
.setting-wrapper {
  position: absolute;
  bottom: px2rem(48);
  background: #fff;
  left: 0;
  width: 100%;
  height: px2rem(60);
  box-shadow: 0 px2rem(-8) px2rem(8) rgba(0, 0, 0, 0.15);
  z-index: 102;
  .setting-font-size {
    display: flex;
    height: 100%;
    .preview {
      flex: 0 0 px2rem(40);
      @include center;
    }
    .select {
      display: flex;
      flex: 1;
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
              border-top: none;
            }
          }
        }
        .line {
          flex: 1;
          height: 0;
          border-top: px2rem(1) solid #ccc;
        }
        .point-wrapper {
          position: relative;
          flex: 0 0 0;
          width: 0;
          height: px2rem(7);
          border-left: px2rem(1) solid #ccc;
          .point {
            position: absolute;
            top: px2rem(-8);
            left: px2rem(-10);
            width: px2rem(20);
            height: px2rem(20);
            border-radius: 50%;
            background: #fff;
            border: px2rem(1) solid #ccc;
            box-shadow: 0 px2rem(4) px2rem(4) rgba(0, 0, 0, 0.15);
            @include center;
            .small-point {
              width: px2rem(5);
              height: px2rem(5);
              background: #000;
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
      flex: 1;
      display: flex;
      flex-direction: column;
      padding: px2rem(5);
      box-sizing: border-box;
      .preview {
        flex: 1;
        border: px2rem(1) solid #ccc;
        &.no-border {
          border: none;
        }
      }
      .text {
        flex: 0 0 px2rem(30);
        font-size: px2rem(16);
        color: #ccc;
        @include center;
        &.selected {
          color: #333;
        }
      }
    }
  }
  .setting-progress {
    position: relative;
    width: 100%;
    height: 100%;
    .progress-wrapper {
      width: 100%;
      height: 100%;
      @include center;
      padding: 0 px2rem(30);
      box-sizing: border-box;
      .progress {
        width: 100%;
        /* cover default CSS */
        -webkit-appearance: none;
        height: px2rem(2);
        background: -webkit-linear-gradient(#999, #999) no-repeat #ddd;
        background-size: 0 100%;
        &:focus {
          outline: none;
        }
        &::-webkit-slider-thumb {
          -webkit-appearance: none;
          height: px2rem(20);
          width: px2rem(20);
          border-radius: 50%;
          background: #fff;
          border: px2rem(1) solid #ddd;
          box-shadow: 0 4px 4px 0 rgba(0, 0, 0, 0.15);
        }
      }
    }
    //show percentage fontsize
    .text-wrapper {
      position: absolute;
      left: 0;
      bottom: 0;
      width: 100%;
      color: #333;
      font-size: px2rem(22);
      text-align: center;
      span {
        font-size: px2rem(14);
        @include center;
      }
    }
  }
}
.content-mask {
  position: absolute;
  top: 0;
  left: 0;
  z-index: 101;
  display: flex;
  width: 100%;
  height: 100%;
  background: rgba(51, 51, 51, 0.8);
}
</style>
