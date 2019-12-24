<template>
  <!-- 用户可见的容器高度可能只有 300px -->
  <div
    class="container"
    style="width: 200px; height: 300px;"
    @scroll.passive="handleScroll"
  >
    <!-- 总的列表 div ，用于撑起列表的高度 -->
    <div
      class="total-list"
      :style="{
        height: `${itemHeight * data.length}px`,
      }"
    >
      <div
        class="visible-list"
        :style="{
        transform: `translateY(${topHeight}px)`,
      }"
      >
        <div
          v-for="item in visibleList"
          :key="item.id"
          class="visible-list-item"
          :style="{
          height: `${itemHeight}px`,
        }"
        >{{ item.value }}</div>
      </div>
    </div>

    <!-- 此处只需渲染可见列表即可，无需渲染全部数据 -->

  </div>
</template>

<script>
// 简单虚拟列表实现

export default {
  name: 'FixedTotalHeight',
  props: {
    /** 数据 */
    data: {
      type: Array,
      default: () => [],
    },

    /** 列表项最低高度为 30 */
    itemHeight: {
      type: Number,
      default: 30,
    },
  },
  data () {
    return {
      /** 可见的列表 */
      visibleList: [],

      /** 已滚过的高度 */
      topHeight: 0,
    }
  },
  methods: {
    /** 更新可见列表 */
    updateVisibleList () {
      const scrollTop = this.$el.scrollTop
      const scrollHeight = this.$el.scrollHeight
      const clientHeight = this.$el.clientHeight
      const itemCount = this.data.length
      const scrollTopMax = scrollHeight - clientHeight
      /** 进度条滚动百分比 */
      const scrollPtg = scrollTop / scrollTopMax
      /** 确定定位项 */
      const itemIndex = Math.floor(scrollPtg * itemCount);
      /** 可见列表项个数 = 可见容器高度 / 每个列表项高度 ，记得向上取整 */
      const visibleCount = Math.ceil(this.$el.clientHeight / this.itemHeight)
      /** 确定起始项和结束项 */
      const startIndex = Math.max(0, itemIndex - Math.ceil(scrollPtg * visibleCount))
      const endIndex = Math.min(itemCount - 1, itemIndex + Math.ceil((1 - scrollPtg) * visibleCount))
      /** 确定定位项偏移比例 */
      const itemOffsetPtg = scrollPtg * itemCount - itemIndex;
      /** 赋值可见列表 */
      this.visibleList = this.data.slice(startIndex, endIndex + 1)
      /** 偏移量 = scrollTop + 滚动过的视口高度 - 定位项偏移高度 - 起始项至定位项的高度 */
      const topHeight = scrollTop + scrollPtg * clientHeight - itemOffsetPtg * this.itemHeight - (itemIndex - startIndex) * this.itemHeight
      // eslint-disable-next-line
      console.log({scrollTop,clientPtgHeight:scrollPtg * clientHeight,itemOffsetHeight:itemOffsetPtg * this.itemHeight,s2iHeight:(itemIndex - startIndex) * this.itemHeight})
      /** 更新已滚过的高度 */
      this.topHeight = topHeight

    },

    handleScroll () {
      this.updateVisibleList()
    }
  },
  mounted () {
    this.updateVisibleList()
  },
  watch: {
    data () {
      this.updateVisibleList()
    },
  },
}
</script>

<style scoped>
.container {
  border: 1px solid red;
  margin: 0 auto;
  overflow-y: auto;
  position: relative;
}

.total-list {
  box-sizing: border-box;
  width: 100%;
  border: 1px solid green;
  position: relative;
  overflow: hidden;
}

.visible-list {
  box-sizing: border-box;
  width: 100%;
  position: absolute;
  top: 0;
}

.visible-list-item {
  box-sizing: border-box;
  width: 100%;
  border: 1px solid blue;
  padding: 10px;
}
</style>
