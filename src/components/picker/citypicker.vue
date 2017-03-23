<template lang="pug">
  q-modal(ref="cityPickerModal",position="bottom")
    .row.padding.border-bottom-line
      .auto
        button.default.small(@click="valFunc('close')") 取消
      .auto.text-right
        button.primary.small.outline(@click="confirmFunc") 确定
    .row.wheel-picker
      .middle-line
      .auto.text-center.picker-part(v-touch-swipe="handlerProv",:style="{transform: `translate3d(0,${provTranslateY}px,0)`}")
        picker-item(:items="provItems", :wheelIndex="provIndex")
      .auto.text-center.picker-part(v-touch-swipe="handlerCity",:style="{transform: `translate3d(0,${cityTranslateY}px,0)`}")
        picker-item(:items="cityItems", :wheelIndex="cityIndex")
      .auto.text-center.picker-part(v-touch-swipe="handlerArea",:style="{transform: `translate3d(0,${areaTranslateY}px,0)`}")
        picker-item(:items="areaItems", :wheelIndex="areaIndex")

</template>

<script>
  import PickerItem from './pickeritem.vue'
  import cityData from './city'

  export default {
    data () {
      return {
        choosedVal: '',
        provTranslateY: 42,
        cityTranslateY: 42,
        areaTranslateY: 42,
        provIndex: 1,
        cityIndex: 1,
        areaIndex: 1,
        provVal: '',
        cityVal: '',
        areaVal: '',
        totalItems: [],
        provItems: [],
        cityItems: [],
        areaItems: [],
        itemHeight: 42,
        addrData: cityData
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
      }
    },
    components: {
      PickerItem
    },
    watch: {
      isOpen (nv, ov) {
        if (nv) {
          this.$refs.cityPickerModal.open()
        }
        else {
          this.$refs.cityPickerModal.close()
        }
      }
    },
    beforeMount () {
      // init province array
      const me = this
      this.addrData.map((item) => {
        me.provItems.push(item.name)
      })
      // init city array
      this.addrData[0].sub.map((ctItem) => {
        me.cityItems.push(ctItem.name)
      })
      this.provVal = this.provItems[0]
      this.cityVal = this.cityItems[0]
    },
    computed: {
    },
    methods: {
      confirmFunc () {
        if (this.cityVal !== '' && this.areaVal !== '') {
          this.choosedVal = `${this.provVal} ${this.cityVal} ${this.areaVal}`
        }
        else if (this.cityVal !== '') {
          this.choosedVal = `${this.provVal} ${this.cityVal}`
        }
        else {
          this.choosedVal = `${this.provVal}`
        }
        this.valFunc(this.choosedVal)
      },
      handlerProv (obj) {
        this.cityTranslateY = this.itemHeight
        this.cityIndex = 1
        this.areaTranslateY = this.itemHeight
        this.areaIndex = 1
        this.handler(obj, 'provItems', 'provTranslateY', 'provIndex', 'provVal')
      },
      handlerCity (obj) {
        this.areaTranslateY = this.itemHeight
        this.areaIndex = 1
        this.handler(obj, 'cityItems', 'cityTranslateY', 'cityIndex', 'cityVal')
      },
      handlerArea (obj) {
        this.handler(obj, 'areaItems', 'areaTranslateY', 'areaIndex', 'areaVal')
      },
      handler (obj, currentItemName, translateYName, choosedIndexName, selectValName) {
        const me = this
        let diff = obj.distance.y / this.itemHeight
        let cindex = Math.round(diff)
        const totalMin = (this[currentItemName].length - 2) * this.itemHeight
        if (obj.direction === 'up') {
          this[translateYName] -= obj.distance.y
          this[choosedIndexName] += cindex
          this[translateYName] = 0 - ((this[choosedIndexName] - 2) * this.itemHeight)
          if (this[translateYName] < -totalMin) {
            this[translateYName] = -totalMin
            this[choosedIndexName] = this[currentItemName].length
          }
        }
        if (obj.direction === 'down') {
          this[translateYName] = this[translateYName] + (cindex * this.itemHeight)
          this[choosedIndexName] -= cindex
          if (this[translateYName] > this.itemHeight) {
            this[translateYName] = this.itemHeight
            this[choosedIndexName] = 1
          }
        }
        this[selectValName] = this[currentItemName][this[choosedIndexName] - 1]

        // dynamic update city/area array
        if (currentItemName === 'provItems') {
          this.areaVal = ''
          this.cityIndex = 1
          this.areaIndex = 1
          this.cityItems = []
          this.areaItems = []
          this.cityVal = ''
          if (this.addrData[this.provIndex - 1].hasOwnProperty('sub')) {
            this.addrData[this.provIndex - 1].sub.map((item) => {
              me.cityItems.push(item.name)
            })
            this.cityVal = this.cityItems[0]
            if (this.addrData[this.provIndex - 1].sub[this.cityIndex - 1].hasOwnProperty('sub')) {
              this.addrData[this.provIndex - 1].sub[this.cityIndex - 1].sub.map((area) => {
                me.areaItems.push(area.name)
              })
              this.areaVal = this.areaItems[0]
            }
          }
        }

        if (currentItemName === 'cityItems') {
          this.areaIndex = 1
          this.areaVal = ''
          this.areaItems = []
          if (this.addrData[this.provIndex - 1].sub[this.cityIndex - 1].hasOwnProperty('sub')) {
            this.addrData[this.provIndex - 1].sub[this.cityIndex - 1].sub.map((area) => {
              me.areaItems.push(area.name)
            })
            this.areaVal = this.areaItems[0]
          }
        }
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
