<template lang="pug">
  q-modal(ref="cityPickerModal",position="bottom", @close="valFunc('close')")
    .row.padding.border-bottom-line
      .auto
        button.default.small.wheel-gray(@click="valFunc('close')") 取消
      .auto.text-right
        button.primary.small.outline(@click="confirmFunc") 确定
    .row.wheel-picker
      .part-top
      .part-bottom
      .middle-line
      .auto.text-center.picker-part(v-touch-swipe="handlerProv",:style="{transform: `translate3d(0,${provTranslateY}px,0)`}")
        picker-item(:items="provItems", :wheelIndex="provIndex", style="transition-timing-function: ease", :itemColor="'#0180ff'")
      .auto.text-center.picker-part(v-touch-swipe="handlerCity",:style="{transform: `translate3d(0,${cityTranslateY}px,0)`}")
        picker-item(:items="cityItems", :wheelIndex="cityIndex", style="transition-timing-function: ease", :itemColor="'#0180ff'")
      .auto.text-center.picker-part(v-touch-swipe="handlerArea",:style="{transform: `translate3d(0,${areaTranslateY}px,0)`}")
        picker-item(:items="areaItems", :wheelIndex="areaIndex", style="transition-timing-function: ease", :itemColor="'#0180ff'")

</template>

<script>
  import PickerItem from './pickeritem.vue'
  import cityData from './city'

  export default {
    data () {
      return {
        choosedVal: '',
        provTranslateY: 84,
        cityTranslateY: 84,
        areaTranslateY: 84,
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
        this.cityTranslateY = this.itemHeight * 2
        this.cityIndex = 1
        this.areaTranslateY = this.itemHeight * 2
        this.areaIndex = 1
        this.handler(obj, 'provItems', 'provTranslateY', 'provIndex', 'provVal')
      },
      handlerCity (obj) {
        this.areaTranslateY = this.itemHeight * 2
        this.areaIndex = 1
        this.handler(obj, 'cityItems', 'cityTranslateY', 'cityIndex', 'cityVal')
      },
      handlerArea (obj) {
        this.handler(obj, 'areaItems', 'areaTranslateY', 'areaIndex', 'areaVal')
      },
      handler (obj, currentItemName, translateYName, choosedIndexName, selectValName) {
        const me = this
        let diff = 0
        if (obj.distance.y > (this.itemHeight / 2)) {
          diff = obj.distance.y / this.itemHeight
        }
        let cindex = Math.round(diff)
        const totalMin = (this[currentItemName].length - 3) * this.itemHeight
        if (obj.direction === 'up') {
          this[translateYName] = this[translateYName] - obj.distance.y
          setTimeout(function () {
            me[choosedIndexName] += cindex
            me[translateYName] = 0 - ((me[choosedIndexName] - 3) * me.itemHeight)
            if (me[translateYName] < -totalMin) {
              me[translateYName] = -totalMin
              me[choosedIndexName] = me[currentItemName].length
            }
          }, obj.duration)
        }
        if (obj.direction === 'down') {
          me[translateYName] = me[translateYName] + obj.distance.y
          setTimeout(function () {
            me[choosedIndexName] -= cindex
            me[translateYName] = me[translateYName] - obj.distance.y + cindex * me.itemHeight
            if (me[translateYName] > me.itemHeight * 2) {
              me[translateYName] = me.itemHeight * 2
              me[choosedIndexName] = 1
            }
          }, obj.duration)
        }
        setTimeout(function () {
          me[selectValName] = me[currentItemName][me[choosedIndexName] - 1]
        }, obj.duration)

        // dynamic update city/area array
        setTimeout(function () {
          if (currentItemName === 'provItems') {
            me.areaVal = ''
            me.cityIndex = 1
            me.areaIndex = 1
            me.cityItems = []
            me.areaItems = []
            me.cityVal = ''
            if (me.addrData[me.provIndex - 1].hasOwnProperty('sub')) {
              me.addrData[me.provIndex - 1].sub.map((item) => {
                me.cityItems.push(item.name)
              })
              me.cityVal = me.cityItems[0]
              if (me.addrData[me.provIndex - 1].sub[me.cityIndex - 1].hasOwnProperty('sub')) {
                me.addrData[me.provIndex - 1].sub[me.cityIndex - 1].sub.map((area) => {
                  me.areaItems.push(area.name)
                })
                me.areaVal = me.areaItems[0]
              }
            }
          }

          if (currentItemName === 'cityItems') {
            me.areaIndex = 1
            me.areaVal = ''
            me.areaItems = []
            if (me.addrData[me.provIndex - 1].sub[me.cityIndex - 1].hasOwnProperty('sub')) {
              me.addrData[me.provIndex - 1].sub[me.cityIndex - 1].sub.map((area) => {
                me.areaItems.push(area.name)
              })
              me.areaVal = me.areaItems[0]
            }
          }
        }, obj.duration)
      }
    }
  }

</script>

<style type="text/css",scoped>
  @import url("./picker.css");
</style>
