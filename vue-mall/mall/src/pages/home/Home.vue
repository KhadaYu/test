<!--  -->
<template>
  <div class="home">
    <header class="g-header-container">
      <home-header :class="{'header-transition': isHeaderTransition}" ref="header"></home-header>
    </header>
    <me-scroll
      :data="recommends"
      pullDown
      pullUp
      @pull-down="pullToRefresh"
      @pull-up="pullToLoadMore"
      @scroll="scroll"
      @scroll-end="scrollEnd"
      @pull-down-transition-end="pullDownTransitionEnd"
      ref="scroll"
    >
      <home-slider ref="slider"></home-slider>
      <home-nav></home-nav>
      <home-recommend @loaded="getRecommends" ref="recommend"></home-recommend>
    </me-scroll>
    <div class="g-backtop-container">
      <me-backtop :visible="isBacktopVisible" @backtop="backToTop"></me-backtop>
    </div>
    <router-view></router-view>
  </div>

</template>

<script>
  import HomeHeader from './HomeHeader';
  import HomeSlider from './HomeSlider';
  import MeScroll from 'base/scroll/MeScroll';
  import MeBacktop from 'base/backtop';
  import HomeNav from './nav';
  import HomeRecommend from './recommend';
  import {HEADER_TRANSITION_HEIGHT} from './config';

  export default {
    name: 'Home',
    components: {
      HomeHeader,
      HomeSlider,
      MeScroll,
      HomeNav,
      HomeRecommend,
      MeBacktop,
      HEADER_TRANSITION_HEIGHT
    },
    data() {
      return {
        recommends: [],
        isBacktopVisible: false,
        isHeaderTransition: false
      };
    },
    /* created() {
      setTimeout(() => {
        this.isBacktopVisible = true;
      },1000)
    }, */
    methods: {
      updateScroll() {

      },
      getRecommends(recommends) {
        this.recommends = recommends;
      },
      pullToRefresh(end) {
        this.$refs.slider.update().then(end);
      },
      pullToLoadMore(end) {
        this.$refs.recommend.update()
          .then(end)
          .catch(err => {
            if (err) {
              console.log(err);
            }
            end();
        });
      },
      scroll(translate) {
        this.changeHeaderStatus(translate);
      },
      scrollEnd(translate, scroll, pulling) {
        if (!pulling) {
          this.changeHeaderStatus(translate);
        }
        this.isBacktopVisible = translate < 0 && -translate > scroll.height;
      },
      pullDownTransitionEnd() {
        this.$refs.header.show();
      },
      backToTop() {
        this.$refs.scroll && this.$refs.scroll.scrollToTop();
      },
      changeHeaderStatus(translate) {
        if (translate > 0) {
          this.$refs.header.hide();
          return;
        }

        this.$refs.header.show();
        this.isHeaderTransition = -translate > HEADER_TRANSITION_HEIGHT;
      }
    }
  };
</script>
<style lang='scss' scoped>
  @import "~assets/scss/mixins";
  .home {
    overflow: hidden;
    width: 100%;
    height: 100%;
    background-color: $bgc-theme;
  }
</style>
