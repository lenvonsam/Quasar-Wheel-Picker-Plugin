<template lang="pug">
  div
    .picker-item.padding(v-for="(i,index) in items", :style="{height: `${itemHeight}px`}")
      transition(name="picker-modal")
        div(v-if="(index+1)==wheelIndex",:style="{'color': itemColor}") {{i}}
        div(v-else) {{i}}
</template>

<script>
  export default {
    props: {
      items: {
        type: Array,
        required: true
      },
      wheelIndex: {
        type: Number,
        required: true
      },
      itemHeight: {
        type: Number,
        required: false,
        default: 42
      },
      itemColor: {
        type: String,
        required: false,
        default: '#fff'
      }
    }
  }
</script>

<style type="text/css",scoped>
  .picker-item {
    overflow: hidden;
    transition: all .3s;
    color: #757575;
  }

  .picker-modal-enter-active,
  .picker-modal-leave-active,
  {
      transition: all 1s ease;
  }

  .picker-modal-enter,
  .picker-modal-leave-to,
  {
      transform: translateY(50px);

      opacity: 0;
  }
</style>
