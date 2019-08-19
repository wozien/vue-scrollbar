<template>
  <div class="vertical-scrollbar" v-if="height < 100">
    <div class="thumb" :style="{ height: height + '%', top: top + 'px' }" @mousedown="startDrag"></div>
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

  computed: {
    top() {
      return Math.round((this.scrolling * this.wrapper.height) / 100);
    },
    height() {
      return (this.wrapper.height / this.area.height) * 100;
    }
  },

  methods: {
    startDrag(e) {
      this.start = e.clientY;
      this.draging = true;
    },

    onDrag(e) {
      if (this.draging) {
        e.preventDefault();
        const yMove = e.clientY - this.start;
        const yMovePrecent = (yMove / this.wrapper.height) * 100;
        this.start = e.clientY;
        const next = this.scrolling + yMovePrecent;
        this.onChangePosition(next, 'vertical');
        this.$emit('draging');
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
