<template>
  <div id="app">
    <div class="tab-switch">
      <button :class="{ active: tab === 'NormalList' }" @click="tab = 'NormalList'">定高普通列表</button>
      <button :class="{ active: tab === 'VirtualList' }" @click="tab = 'VirtualList'">定高虚拟列表</button>
      <button :class="{ active: tab === 'PropHeight' }" @click="tab = 'PropHeight'">不定高 Prop 传入高度的虚拟列表</button>
      <button :class="{ active: tab === 'PerfPropHeight' }" @click="tab = 'PerfPropHeight'">算法优化后的不定高 Prop 传入高度的虚拟列表</button>
      <button :class="{ active: tab === 'VirtualListSetData' }" @click="tab = 'VirtualListSetData'">使用方法设置数据的虚拟列表</button>
      <button :class="{ active: tab === 'FixedTotalHeight' }" @click="tab = 'FixedTotalHeight'">固定总高度的虚拟列表</button>
    </div>
    <component
      ref="list"
      :is="tab"
      :data="data"
      :itemHeightGetter="itemHeightGetter"
    />
  </div>
</template>

<script>
/* eslint-disable no-console */
/**
 * NormalList: 定高的普通列表
 * VirtualList: 定高的虚拟列表
 * PropHeight: 不定高、通过 Prop 传入高度的虚拟列表
 */
import NormalList from './components/NormalList.vue'
import VirtualList from './components/VirtualList.vue'
import PropHeight from './components/PropHeight.vue'
import PerfPropHeight from './components/PerfPropHeight.vue'
import VirtualListSetData from './components/VirtualListSetData.vue'
import FixedTotalHeight from  './components/FixedTotalHeight.vue'

const data = []
for (let i = 0; i < 100000; i++) {
  data.push({
    id: Math.random().toString(36).substr(2),
    index: i,
    value: i + 1,
  })
}

export default {
  name: 'app',
  components: {
    NormalList,
    VirtualList,
    PropHeight,
    PerfPropHeight,
    VirtualListSetData,
    FixedTotalHeight,
  },
  data () {
    return {
      data: [],

      tab: 'FixedTotalHeight',
    }
  },
  methods: {
    itemHeightGetter (index) {
      return 40 + (index % 10) * (Math.random() < 0.5 ? -1 : 1)
    },

    setData () {
      this.$refs.list.setData(data)
    },
  },
  watch: {
    tab: {
      immediate: true,
      handler () {
        this.$nextTick(() => {
          if (this.tab === 'VirtualListSetData') {
            this.data = []
            this.setData()
          } else {
            this.data = data
          }
        })
      },
    },
  },
}
</script>

<style>
* {
  margin: 0;
  padding: 0;
}

#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
}

.tab-switch {
  width: 100%;
  height: 80px;
  padding: 10px;
  box-sizing: border-box;
}

.tab-switch button {
  cursor: pointer;
  margin-right: 20px;
  min-width: 100px;
  min-height: 38px;
  margin-right: 30px;
  margin-bottom: 10px;
  border-radius: 5px;
  background: lightcyan;
}

.tab-switch button:focus {
  outline: none;
}

.tab-switch .active {
  background: lightblue;
}
</style>
