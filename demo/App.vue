<template>
  <div id="app">
    <vue-scrollbar ref="scroll" show-type="hover">
      <div>
        <div v-for="item in arr" :key="item" class="item">{{ item }}</div>
      </div>
    </vue-scrollbar>
  </div>
</template>

<script>
import VueScrollbar from '../src/index';

export default {
  data() {
    return {
      arr: [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
    };
  },

  components: {
    VueScrollbar
  }
};
</script>

<style lang="scss">
#app {
  width: 400px;
  height: 600px;
  margin: 0 auto;
  .item {
    height: 100px;
    line-height: 100px;
    text-align: center;
    background: lightskyblue;
    margin: 10px 0px;
  }
}
</style>

