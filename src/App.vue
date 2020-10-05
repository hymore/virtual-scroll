<template>
  <div id="app">
    <virtual-list
      :size="80"
      :remain="8"
      :list="list"
      v-slot="{ item }"
      :variable="true"
    >
      <scroll-item :item="item"></scroll-item>
    </virtual-list>
  </div>
</template>

<script>
import VirtualList from "./components/virtual-list.vue";
import ScrollItem from "./components/scroll-item";
import Mock from "mockjs";
let list = [];
for (let i = 0; i < 1000; i++) {
  list.push({ id: i, value: Mock.Random.sentence(5, 50) });
}
export default {
  components: {
    VirtualList,
    ScrollItem
  },
  data() {
    return {
      list
    };
  }
};
</script>

<style>
* {
  padding: 0;
  margin: 0;
  box-sizing: border-box;
}
::-webkit-scrollbar {
  width: 5px;
  height: 5px;
}

::-webkit-scrollbar-thumb {
  border-radius: 1em;
  background-color: rgba(50, 50, 50, 0.3);
}

::-webkit-scrollbar-track {
  border-radius: 1em;
  background-color: rgba(50, 50, 50, 0.1);
}
</style>
