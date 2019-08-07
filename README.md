基于 vue 实现的 pc 端滚动条组件

## 开始使用

### 下载

```bash
npm install @wozien/vue-scrollbar
```

### 引入组件

```html
<template>
  <vue-scrollbar :show-type="hover" ref="scroll">
    <!-- 滚动内容包含在一个块内 -->
    <div class="container">
      <!-- 滚动内容 -->
      <div class="item"></div>
      <div class="item"></div>
      <div class="item"></div>
      <!-- ... -->
    </div>
  </vue-scrollbar>
</template>

<script>
  import VueScrollbar from '@wozien/vue-scrollbar';

  export default {
    mounted() {
      this.$ref.scroll.scrollTo(0, 100);
    },

    components: {
      VueScrollbar
    }
  };
</script>
```

## 说明

### 属性

<dl>
  <dt>show-type:String: 滚动条显示方式，默认为一直显示</dt>
  <dd>hide: 隐藏</dd>
  <dd>hover: 移入显示</dd>

### 方法

<dl>
  <dt>scrollTo(x,y): 移动到指定位置</dt>
  <dd>x: 横坐标</dd>
  <dd>y: 纵坐标</dd>
</dl>

<dl>
  <dt>refresh(): 刷新，重新计算高度</dt>
</dl>

<dl>
  <dt>scrollTop(): 移动最顶部</dt>
</dl>

<dl>
  <dt>scrollBottom(): 移动最底部</dt>
</dl>

<dl>
  <dt>scrollLeft(): 移动最左边</dt>
</dl>

<dl>
  <dt>scrollRight(): 移动最右边</dt>
</dl>
