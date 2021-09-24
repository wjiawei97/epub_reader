<template>
  <div class="content">
    <div class="content-wrapper"
         v-if="bookAvailable">
      <div class="content-item"
           v-for="(item,index) in navigation.toc"
           :key="index"
           @click="jumpTo(item.href)">
        <span class="text">{{item.label}}</span>
      </div>

    </div>
    <div class="loading"
         v-else>Loading...</div>
  </div>
</template>

<script>
export default {
  props: {
    navigation: Object,
    bookAvailable: Boolean
  },
  methods: {
    jumpTo (href) {
      this.$emit('jumpTo', href)
    }
  }
}
</script>

<style lang="scss" scoped>
@import "../assets/styles/global";
.content {
  position: absolute;
  top: 0;
  left: 0;
  z-index: 102;
  width: 80%;
  height: 100%;
  background-color: #fff;
  .content-wrapper {
    width: 100%;
    height: 100%;
    .content-item {
      border-bottom: px2rem(1) solid #ccc;
      padding: 0 0 px2rem(5) px2rem(15);
      .text {
        font-size: px2rem(12);
        color: #333;
      }
    }
  }
  .loading {
    width: 100%;
    height: 100%;
    font-size: px2rem(14);
    color: #333;
    @include center;
  }
}
</style>
</style>>
