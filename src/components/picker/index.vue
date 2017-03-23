<template lang="pug">
  q-modal(ref="pickerModel",position="bottom")
    .row.padding.border-bottom-line
      .auto
        button.default.small(@click="valFunc('close')") 取消
      .auto.text-right
        button.primary.small.outline(@click="confirmFunc") 确定
    .row.wheel-picker
      .middle-line
      .auto.text-center.picker-part(v-touch-swipe="handler1",:style="{transform: `translate3d(0,${currenTranslateY1}px,0)`}")
        picker-item(:items="pickerItems[0]", :wheelIndex="wheelIndex1")
      .auto.text-center.picker-part(v-touch-swipe="handler2",:style="{transform: `translate3d(0,${currenTranslateY2}px,0)`}", v-if="showSecPicker")
        picker-item(:items="pickerItems[1]", :wheelIndex="wheelIndex2")
      .auto.text-center.picker-part(v-touch-swipe="handler3",:style="{transform: `translate3d(0,${currenTranslateY3}px,0)`}", v-if="showThirdPicker")
        picker-item(:items="pickerItems[2]", :wheelIndex="wheelIndex3")
</template>

<script>
  import PickerItem from './pickeritem.vue'
  export default {
    data () {
      return {
        choosedVal: '',
        currenTranslateY1: 42,
        currenTranslateY2: 42,
        currenTranslateY3: 42,
        wheelIndex1: 1,
        wheelIndex2: 1,
        wheelIndex3: 1,
        pickerVal1: '',
        pickerVal2: '',
        pickerVal3: '',
        showSecPicker: false,
        showThirdPicker: false,
        itemHeight: 42
      }
    },
    props: {
      isOpen: {
        type: Boolean,
        required: true
      },
      valFunc: {
        type: Function,
        required: true
      },
      pickerItems: {
        type: Array,
        required: true
      }
    },
    components: {
      PickerItem
    },
    watch: {
      isOpen (nv, ov) {
        if (nv) {
          this.$refs.pickerModel.open()
        }
        else {
          this.$refs.pickerModel.close()
        }
      }
    },
    beforeMount () {
      if (this.pickerItems[0].length > 0) {
        this.pickerVal1 = this.pickerItems[0][0]
      }
      if (this.pickerItems.length === 2) {
        if (this.pickerItems[1].length > 0) {
          this.pickerVal2 = this.pickerItems[1][0]
        }
        this.showSecPicker = true
      }
      if (this.pickerItems.length === 3) {
        if (this.pickerItems[1].length > 0) {
          this.pickerVal2 = this.pickerItems[1][0]
        }
        this.showSecPicker = true

        if (this.pickerItems[2].length > 0) {
          this.pickerVal3 = this.pickerItems[2][0]
        }
        this.showThirdPicker = true
      }
    },
    methods: {
      confirmFunc () {
        if (this.pickerItems.length === 1) {
          this.choosedVal = this.pickerVal1
        }
        else if (this.pickerItems.length === 2) {
          this.choosedVal = `${this.pickerVal1} ${this.pickerVal2}`
        }
        else {
          this.choosedVal = `${this.pickerVal1} ${this.pickerVal2} ${this.pickerVal3}`
        }
        this.valFunc(this.choosedVal)
      },
      handler1 (obj) {
        this.handler(obj, 1)
      },
      handler2 (obj) {
        this.handler(obj, 2)
      },
      handler3 (obj) {
        this.handler(obj, 3)
      },
      handler (obj, pickItemIndex) {
        let diff = obj.distance.y / this.itemHeight
        let cindex = Math.round(diff)
        const totalMin = (this.pickerItems[pickItemIndex - 1].length - 2) * this.itemHeight
        if (obj.direction === 'up') {
          this['wheelIndex' + pickItemIndex] += cindex
          this['currenTranslateY' + pickItemIndex] = 0 - ((this['wheelIndex' + pickItemIndex] - 2) * this.itemHeight)
          if (this['currenTranslateY' + pickItemIndex] < -totalMin) {
            this['currenTranslateY' + pickItemIndex] = -totalMin
            this['wheelIndex' + pickItemIndex] = this.pickerItems[pickItemIndex - 1].length
          }
        }

        if (obj.direction === 'down') {
          this['currenTranslateY' + pickItemIndex] += cindex * this.itemHeight
          this['wheelIndex' + pickItemIndex] -= cindex
          if (this['currenTranslateY' + pickItemIndex] > this.itemHeight) {
            this['currenTranslateY' + pickItemIndex] = this.itemHeight
            this['wheelIndex' + pickItemIndex] = 1
          }
        }
        this['pickerVal' + pickItemIndex] = this.pickerItems[pickItemIndex - 1][this['wheelIndex' + pickItemIndex] - 1]
      }
    }
  }

</script>

<style type="text/css",scoped>
  .wheel-picker {
    height:126px;
    overflow:hidden;
    position:relative;
  }

  .wheel-picker .picker-part {
    transition: transform 1s ease;
    flex-direction: column;
  }

  .wheel-picker .middle-line {
    position:absolute;
    background:rgba(0,0,0,.3);
    height:42px;
    top:42px;
    left:0;
    right:0;
    box-shadow:0 4px 5px 0 rgba(0,0,0,.14), 0 1px 10px 0 rgba(0,0,0,.12), 0 2px 4px -1px rgba(0,0,0,.4);
  }



</style>
