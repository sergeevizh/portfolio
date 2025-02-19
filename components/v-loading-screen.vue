<template>
  <div class="v-loading-screen">
    <div class="v-loading-screen__wrapp">
      <div class="v-loading-screen__title h4">
        <div class="mask-text mask-text--1 js-splitme">Сидоров Александр</div>
      </div>
    </div>
    <!-- resize -->
    <resize-observer @notify="handleResize" />
  </div>
</template>

<script>
import { TimelineMax, TweenMax, Power0, Power1 } from "gsap";
import { mapState, mapActions, mapGetters } from "vuex";
import Splitter from "split-html-to-chars";

export default {
  name: "v-loading-screen",
  computed: {
    ...mapState(["firstScreen"]),
    ...mapGetters(["getMobile", "getDesktop"])
  },
  mounted() {
    if (this.firstScreen) {
      this.animLoadScreen();
    } else {
      this.screenFirst();
    }
    if (this.getMobile) {
      this.handleResize();
    }
  },
  methods: {
    ...mapActions(["setScreen", "setFirstScreen"]),
    animLoadScreen() {
      const self = this;
      const splits = document.querySelectorAll(".js-splitme");

      [].forEach.call(splits, function(el) {
        el.outerHTML = Splitter(el.outerHTML, '<span class="letter">$</span>');
      });

      TweenMax.set([".mask-text", ".loading-screen__subtitle"], {
        visibility: "visible"
      });

      const tl = new TimelineMax({ onComplete: self.screenFirst });
      tl.staggerFromTo(
        ".mask-text .letter",
        1,
        {
          scaleW: 0,
          x: 50
        },
        {
          delay: 0.4,
          scaleW: 0.1,
          autoAlpha: 1,
          x: 0,
          ease: Power1.easeOut
        },
        0.15
      ).fromTo(
        ".v-loading-screen__wrapp",
        1,
        { autoAlpha: 1 },
        { autoAlpha: 0, y: -20, ease: Power0.easeNone }
      );
    },
    screenFirst() {
      this.setScreen(false);
      this.setFirstScreen(false);
    },
    handleResize() {
      const vh = window.innerHeight * 0.01;
      document.documentElement.style.setProperty("--vh", `${vh}px`);
    }
  }
};
</script>

<style lang="scss">
@import "@/assets/scss/utils/vars";
@import "@/assets/scss/mixins/mixins";
.v-loading-screen {
  height: 100vh;
  height: calc(var(--vh, 1vh) * 100);
  width: 100%;
  z-index: 998;
  display: flex;
  align-items: center;
  background-color: var(--bg-color-rgba);
  overflow: hidden;
  font-family: $mainFontBold;

  @include lg {
    background-color: transparent;
  }

  &__wrapp {
    width: 100%;
    text-align: center;
  }

  &__title {
    font-size: 1.5rem;
    color: var(--white);
    backface-visibility: hidden;
  }
}
.js-splitme {
  visibility: hidden;
  & > * {
    margin-right: 1px;
  }

  &:last-child {
    margin-right: 0;
  }
  & .letter {
    will-change: transform;
    backface-visibility: hidden;
    visibility: hidden;
  }
}
</style>
