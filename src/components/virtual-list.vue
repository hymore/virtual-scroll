<template>
  <div class="viewport" ref="viewport" @scroll="scrollFn">
    <div class="scroll-bar" ref="scrollBar"></div>
    <div class="scroll-list" :style="{ transform: `translateY(${offset}px)` }">
      <div
        v-for="item in visibleData"
        :key="item.id"
        :vid="item.id"
        ref="scrollItem"
      >
        <slot :item="item"></slot>
      </div>
    </div>
  </div>
</template>

<script>
import throttle from "lodash/throttle";
export default {
  props: {
    size: Number,
    remain: Number,
    list: Array,
    variable: Boolean
  },
  computed: {
    visibleData() {
      let start = this.start - this.prevCount;
      let end = this.end + this.nextCount;
      return this.list.slice(start, end);
    },
    prevCount() {
      return Math.min(this.start, this.remain);
    },
    nextCount() {
      return Math.min(this.remain, this.list.length - this.end);
    }
  },
  data() {
    return {
      start: 0,
      end: this.remain,
      offset: 0,
      positions: []
    };
  },
  created() {
    this.scrollFn = throttle(this.scrollHanlder, 300, { leading: false });
  },
  mounted() {
    this.$refs.viewport.style.height = this.size * this.remain + "px";
    this.$refs.scrollBar.style.height = this.size * this.list.length + "px";
    this.cacheList();
  },
  updated() {
    this.$nextTick(() => {
      let nodes = this.$refs["scrollItem"];
      if (nodes && nodes.length) {
        nodes.forEach(node => {
          let { height } = node.getBoundingClientRect();
          let id = node.getAttribute("vid") - 0;
          let oldHeight = this.positions[id].height;
          let val = height - oldHeight;
          if (val) {
            this.positions[id].height = height;
            this.positions[id].bottom = this.positions[id].bottom + val;
            for (let i = id + 1; i < this.positions.length - 1; i++) {
              this.positions[i].top = this.positions[i - 1].bottom + val;
            }
          }
        });
        this.$refs.scrollBar.style.height =
          this.positions[this.positions.length - 1].bottom + "px";
      }
    });
  },
  methods: {
    cacheList() {
      this.positions = this.list.map((item, index) => ({
        height: this.size,
        top: index * this.size,
        bottom: (index + 1) * this.size
      }));
    },
    scrollHanlder() {
      let scrollTop = this.$refs.viewport.scrollTop;
      if (this.variable) {
        //  二分查找
        this.start = this.getStartIndex(scrollTop);
        this.end = this.start + this.remain;
        this.offset = this.positions[this.start - this.prevCount]
          ? this.positions[this.start - this.prevCount].top
          : 0;
      } else {
        this.start = Math.floor(scrollTop / this.size);
        this.end = this.start + this.remain;
        this.offset = (this.start - this.prevCount) * this.size;
      }
    },
    getStartIndex(value) {
      let start = 0;
      let end = this.positions.length - 1;
      let temp = null;
      while (start <= end) {
        let middleIndex = parseInt(start + end);
        let middleValue = this.positions[middleIndex].bottom;
        if (middleValue == value) {
          return middleIndex + 1;
        } else if (middleValue < value) {
          start = middleIndex + 1;
        } else if (middleValue > value) {
          if (temp === null || temp > middleIndex) {
            temp = middleIndex;
          }
          end = middleIndex - 1;
        }
      }
      return temp;
    }
  }
};
</script>

<style scoped>
.viewport {
  overflow-y: scroll;
  position: relative;
}
.scroll-list {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
}
</style>