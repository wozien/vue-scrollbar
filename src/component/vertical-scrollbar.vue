<template>
  <div class="vertical-scrollbar" v-if="height < 100">
    <div class="thumb" :style="{ height: height + '%', top: scrolling + '%' }" @mousedown="startDrag"></div>
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
      height: 0,
      top: 0
    };
  },

  watch: {
    'wrapper.height'(val) {
      this.calSize();
    },
    'area.height'(val) {
      this.calSize();
    }
  },

  methods: {
    calSize() {
      this.height = (this.wrapper.height / this.area.height) * 100;
    },

    startDrag(e) {
      e.preventDefault();
      e.stopPropagation();
      this.start = e.clientY;
      this.draging = true;
    },

    onDrag(e) {
      e.preventDefault();
      e.stopPropagation();

      if (this.draging) {
        this.$emit('draging');
        const yMove = e.clientY - this.start;
        const yMovePrecent = (yMove / this.wrapper.height) * 100;
        this.start = e.clientY;
        const next = this.scrolling + yMovePrecent;
        this.onChangePosition(next, 'vertical');
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
.vertical-scrollbar {
  position: absolute;
  width: 6px;
  height: 100%;
  top: 0;
  right: 1px;
  .thumb {
    position: relative;
    width: 5px;
    border-radius: 3px;
    background: rgba(0, 0, 0, 0.3);
    cursor: default;
  }
}
</style>
