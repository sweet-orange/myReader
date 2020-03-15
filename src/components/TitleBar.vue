<template>
  <transition name="slide-down">
    <div class="title-wrapper" v-show="ifTitleAndMenu">
      <div class="left">
        <span class="icon-back icon"></span>
      </div>
      <div class="right">
        <div class="icon-wrapper">
          <span class="icon-cart icon"></span>
        </div>
        <div class="icon-wrapper">
          <span class="icon-person icon"></span>
        </div>
        <div class="icon-wrapper">
          <span class="icon-more icon"></span>
        </div>
      </div>
    </div>
  </transition>
</template>
<script>
export default {
  props: {
    ifTitleAndMenu: {
      type: Boolean,
      default: false
    }
  }

}
</script>
<style lang="scss" scoped>
@import '../assets/styles/global';
.title-wrapper {
  position: absolute;
  left: 0;
  top: 0;
  z-index: 101;
  width: 100%;
  height: px2rem(48);
  display: flex;
  background: #fff;
  box-shadow: 0 px2rem(8) px2rem(8) rgba(0, 0, 0, 0.15);
  &.slide-down-enter,
  &.slide-down-leave-to {
    transform: translate3d(0, -100%, 0);
  }
  &.slide-down-enter-to,
  &.slide-down-leave {
    transform: translate3d(0, 0, 0);
  }
  &.slide-down-enter-active,
  &.slide-down-leave-active {
    transition: all 0.3s linear;
  }
  .left {
    flex: 0 0 px2rem(60);
    @include center;
  }
  .right {
    flex: 1;
    display: flex;
    justify-content: flex-end;
    .icon-wrapper {
      flex: 0 0 px2rem(50);
      @include center;
      .icon-cart {
        font-size: px2rem(22);
      }
    }
  }
}
</style>
