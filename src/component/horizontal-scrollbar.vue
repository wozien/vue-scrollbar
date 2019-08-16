<template>
  <div class="horizontal-scrollbar" v-if="width < 100">
    <div class="thumb" :style="{ width: width + '%', left: scrolling + '%' }" @mousedown="startDrag"></div>
  </div>
</template>

<script>
export default {
  props: {
    wrapper: Object,
    area: Object,
    scrolling: Number,
    onChangePosition: Function
  },

  data() {
    return {
      width: 0,
      left: 0
    };
  },

  watch: {
    'wrapper.width'(val) {
      this.calSize();
    },
    'area.width'(val) {
      this.calSize();
    }
  },

  methods: {
    calSize() {
      this.width = (this.wrapper.width / this.area.width) * 100;
    },

    startDrag(e) {
      e.stopPropagation();
      this.start = e.clientX;
      this.draging = true;
    },

    onDrag(e) {
      e.stopPropagation();
      if (this.draging) {
        e.preventDefault();
        this.$emit('draging');
        const xMove = e.clientX - this.start;
        const xMovePrecent = (xMove / this.wrapper.width) * 100;
        this.start = e.clientX;
        const next = this.scrolling + xMovePrecent;
        this.onChangePosition(next, 'horizontal');
      }
    },

    stopDrag() {
      if (this.draging) {
        this.draging = false;
        this.$emit('stop-drag');
      }
    }
  },

  mounted() {
    this.calSize();
    document.addEventListener('mousemove', this.onDrag);
    document.addEventListener('mouseup', this.stopDrag);
  },

  beforeDestroy() {
    document.removeEventListener('mousemove', this.onDrag);
    document.removeEventListener('mouseup', this.stopDrag);
  }
};
</script>

<style lang="scss" scoped>
.horizontal-scrollbar {
  position: absolute;
  height: 7px;
  width: 100%;
  left: 0;
  bottom: 1px;
  .thumb {
    position: relative;
    height: 6px;
    border-radius: 3px;
    background: rgba(0, 0, 0, 0.4);
    cursor: default;
  }
}
</style>
