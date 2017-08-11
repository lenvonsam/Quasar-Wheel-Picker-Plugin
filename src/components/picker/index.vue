<template lang="pug">
  q-modal(ref="pickerModel",position="bottom")
    .row.padding.border-bottom-line
      .auto
        button.default.small.wheel-gray(@click="valFunc('close')") 取消
      .auto.text-right
        button.primary.small.outline(@click="confirmFunc") 确定
    .row.wheel-picker
      .middle-line
      .auto.text-center.picker-part(v-touch-swipe="handler1",:style="{transform: `translate3d(0,${currenTranslateY1}px,0)`}")
        picker-item(:items="pickerItems[0]", :wheelIndex="wheelIndex1", style="transition-timing-function: ease", :itemColor="'#0180ff'")
      .auto.text-center.picker-part(v-touch-swipe="handler2",:style="{transform: `translate3d(0,${currenTranslateY2}px,0)`}", v-if="showSecPicker")
        picker-item(:items="pickerItems[1]", :wheelIndex="wheelIndex2", style="transition-timing-function: ease", :itemColor="'#0180ff'")
      .auto.text-center.picker-part(v-touch-swipe="handler3",:style="{transform: `translate3d(0,${currenTranslateY3}px,0)`}", v-if="showThirdPicker")
        picker-item(:items="pickerItems[2]", :wheelIndex="wheelIndex3", style="transition-timing-function: ease", :itemColor="'#0180ff'")
</template>

<script>
  import PickerItem from './pickeritem.vue'
  export default {
    data () {
      return {
        choosedVal: '',
        currenTranslateY1: 84,
        currenTranslateY2: 84,
        currenTranslateY3: 84,
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
        let diff = 0
        if (obj.distance.y > (this.itemHeight / 2)) {
          diff = obj.distance.y / this.itemHeight
        }
        let cindex = Math.round(diff)
        const totalMin = (this.pickerItems[pickItemIndex - 1].length - 3) * this.itemHeight
        const me = this
        if (obj.direction === 'up') {
          this['currenTranslateY' + pickItemIndex] -= obj.distance.y
          setTimeout(function () {
            me['wheelIndex' + pickItemIndex] += cindex
            me['currenTranslateY' + pickItemIndex] = 0 - ((me['wheelIndex' + pickItemIndex] - 3) * me.itemHeight)
            if (me['currenTranslateY' + pickItemIndex] < -totalMin) {
              me['currenTranslateY' + pickItemIndex] = -totalMin
              me['wheelIndex' + pickItemIndex] = me.pickerItems[pickItemIndex - 1].length
            }
          }, obj.duration)
        }

        if (obj.direction === 'down') {
          this['currenTranslateY' + pickItemIndex] += obj.distance.y
          setTimeout(function () {
            me['currenTranslateY' + pickItemIndex] = me['currenTranslateY' + pickItemIndex] - obj.distance.y + cindex * me.itemHeight
            me['wheelIndex' + pickItemIndex] -= cindex
            if (me['currenTranslateY' + pickItemIndex] > me.itemHeight * 2) {
              me['currenTranslateY' + pickItemIndex] = me.itemHeight * 2
              me['wheelIndex' + pickItemIndex] = 1
            }
          }, obj.duration)
        }
        setTimeout(function () {
          me['pickerVal' + pickItemIndex] = me.pickerItems[pickItemIndex - 1][me['wheelIndex' + pickItemIndex] - 1]
        }, obj.duration)
      }
    }
  }

</script>

<style type="text/css",scoped>
  @import url("./picker.css");
</style>
