<template>
  <div class="scroll-item" style="border:1px solid red;padding:20px">
    {{ item.value }}
    hello
  </div>
</template>

<script>
export default {
  props: {
    item: Object
  },
  mounted() {}
};
</script>

<style>
</style>