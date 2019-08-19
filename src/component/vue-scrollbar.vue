<template>
  <div ref="wrapper" :class="'vue-scrollbar' + (classes ? ' classes' : '')" @mouseenter="isHover = true" @mouseleave="isHover = false" @click="calSize">
    <div class="scroll-area" ref="area" :style="{ marginTop: top * -1 + 'px', marginLeft: left * -1 + 'px' }" @mousewheel.stop.prevent="scroll">
      <slot></slot>

      <!-- 垂直滚动条 -->
      <vertical-scrollbar
        v-if="showBar"
        :wrapper="{ height: outerHeight }"
        :area="{ height: innerHeight }"
        :scrolling="vMove"
        :on-change-position="handleChangePosition"
        @draging="onDrag"
        @stop-drag="stopDrag"
      ></vertical-scrollbar>

      <!-- 水平滚动条 -->
      <horizontal-scrollbar
        v-if="showBar"
        :wrapper="{ width: outerWidth }"
        :area="{ width: innerWidth }"
        :scrolling="hMove"
        :on-change-position="handleChangePosition"
        @draging="onDrag"
        @stop-drag="stopDrag"
      >
      </horizontal-scrollbar>
    </div>
  </div>
</template>

<script>
import VerticalScrollbar from './vertical-scrollbar';
import HorizontalScrollbar from './horizontal-scrollbar';

export default {
  name: 'VueScrollbar',

  props: {
    step: {
      type: Number,
      default: 53
    },
    showType: {
      type: String,
      default: ''
    },
    classes: String
  },

  data() {
    return {
      ready: false,
      top: 0,
      left: 0,
      outerHeight: 0,
      outerWidth: 0,
      innerHeight: 0,
      innerWidth: 0,
      vMove: 0,
      hMove: 0,
      isHover: false,
      draging: false,
      maxScrollY: 0,
      maxScrollX: 0
    };
  },

  computed: {
    showBar() {
      if (this.draging) return true;
      if (!this.ready || this.showType === 'hide' || (this.showType === 'hover' && !this.isHover)) {
        return false;
      }
      return true;
    }
  },

  methods: {
    getSize() {
      const wrapper = this.$refs.wrapper;
      const area = this.$refs.area;

      return {
        outerHeight: wrapper.clientHeight,
        outerWidth: wrapper.clientWidth,
        innerHeight: area.children[0].clientHeight,
        innerWidth: area.children[0].clientWidth
      };
    },

    calSize(cb) {
      const size = this.getSize();

      if (
        size.outerHeight !== this.outerHeight ||
        size.innerHeight !== this.innerHeight ||
        size.outerWidth !== this.outerWidth ||
        size.innerWidth !== this.innerWidth
      ) {
        this.outerHeight = size.outerHeight;
        this.outerWidth = size.outerWidth;
        this.innerHeight = size.innerHeight;
        this.innerWidth = size.innerWidth;
        this.maxScrollY = this.innerHeight - this.outerHeight;
        this.maxScrollX = this.innerWidth - this.outerWidth;
        this.ready = true;
      }

      typeof cb === 'function' && cb();
    },

    // 滑轮滚动
    scroll(e) {
      this.calSize(() => {
        const num = this.step;
        const shiftKey = e.shiftKey;
        const scrollY = e.deltaY > 0 ? num : -num;
        let scrollX = e.deltaX > 0 ? num : -num;

        if (shiftKey && e.deltaX == 0) {
          scrollX = e.deltaY > 0 ? num : -num;
        }

        const nextY = this.top + scrollY;
        const nextX = this.left + scrollX;

        const canScrollY = this.innerHeight > this.outerHeight;
        const canScrollX = this.innerWidth > this.outerWidth;

        if (canScrollY && !shiftKey) {
          this.scrollY(nextY);
        }

        if (canScrollX && shiftKey) {
          this.scrollX(nextX);
        }
      });
    },

    // 垂直滚动
    scrollY(next) {
      if (next > this.maxScrollY && this.maxScrollY > 0) {
        next = this.maxScrollY;
      } else if (next < 0) {
        next = 0;
      }

      if (this.top !== next) {
        this.top = next;
        this.vMove = (next / this.innerHeight) * 100;
      }
    },

    // 水平滚动
    scrollX(next) {
      if (next > this.maxScrollX && this.maxScrollX > 0) {
        next = this.maxScrollX;
      } else if (next < 0) {
        next = 0;
      }

      if (this.left !== next) {
        this.left = next;
        this.hMove = (next / this.innerWidth) * 100;
      }
    },

    handleChangePosition(movement, direction) {
      this.calSize(() => {
        let next = movement / 100;
        if (direction === 'vertical') {
          this.scrollY(next * this.innerHeight);
        } else if (direction === 'horizontal') {
          this.scrollX(next * this.innerWidth);
        }
      });
    },

    onDrag() {
      this.draging = true;
    },

    stopDrag() {
      this.draging = false;
    },

    // 对外方法
    refresh() {
      this.calSize();
    },

    scrollTo(x, y) {
      this.calSize(() => {
        this.scrollX(x || 0);
        this.scrollY(y || 0);
      });
    },

    scrollTop() {
      this.calSize(() => {
        this.scrollY(0);
      });
    },

    scrollBottom() {
      this.calSize(() => {
        this.scrollY(this.maxScrollY);
      });
    },

    scrollLeft() {
      this.calSize(() => {
        this.scrollX(0);
      });
    },

    scrollRight() {
      this.calSize(() => {
        this.scrollX(this.maxScrollX);
      });
    }
  },

  mounted() {
    this.calSize();
  },

  components: {
    VerticalScrollbar,
    HorizontalScrollbar
  }
};
</script>

<style lang="scss" scoped>
.vue-scrollbar {
  margin: 0 auto;
  position: relative;
  overflow: hidden;
  height: 100%;
}
</style>

