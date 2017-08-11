<template lang="pug">
  q-modal(ref="pickerDateModal",position="bottom")
    .row.padding.border-bottom-line
      .auto
        button.default.small.wheel-gray(@click="valFunc('close')") 取消
      .auto.text-right
        button.primary.small.outline(@click="confirmFunc") 确定
    .row.wheel-picker
      .part-top
      .part-bottom
      .middle-line
      .auto.text-center.picker-part(v-touch-swipe="handlerYear",:style="{transform: `translate3d(0,${currenTranslateYear}px,0)`}")
        picker-item(:items="yearArr", :wheelIndex="wheelIndexYear", style="transition-timing-function: ease", :itemColor="'#0180ff'")
      .auto.text-center.picker-part(v-touch-swipe="handlerMonth",:style="{transform: `translate3d(0,${currenTranslateYMonth}px,0)`}")
        picker-item(:items="monthArr", :wheelIndex="wheelIndexMonth", style="transition-timing-function: ease", :itemColor="'#0180ff'")
      .auto.text-center.picker-part(v-touch-swipe="handlerDay",:style="{transform: `translate3d(0,${currenTranslateYDay}px,0)`}")
        picker-item(:items="dayArr", :wheelIndex="wheelIndexDay", style="transition-timing-function: ease", :itemColor="'#0180ff'")
</template>

<script>
  import PickerItem from './pickeritem.vue'
  export default {
    data () {
      return {
        choosedVal: '',
        currenTranslateYear: 42,
        currenTranslateYMonth: 42,
        currenTranslateYDay: 42,
        wheelIndexYear: 1,
        wheelIndexMonth: 1,
        wheelIndexDay: 1,
        pickerValYear: '',
        pickerValMonth: '',
        pickerValDay: '',
        itemHeight: 42,
        currentDate: new Date(),
        currentYear: new Date().getFullYear(),
        startYear: 1930,
        yearArr: [],
        monthArr: ['01', '02', '03', '04', '05', '06', '07', '08', '09', '10', '11', '12'],
        dayArr: [],
        // 31days of month
        firstMonths: ['01', '03', '05', '07', '08', '10', '12'],
        // 30days of month
        secMonths: ['02', '04', '06', '09', '11']
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
          this.$refs.pickerDateModal.open()
        }
        else {
          this.$refs.pickerDateModal.close()
        }
      }
    },
    beforeMount () {
      this.initDate()
    },
    methods: {
      initDate () {
        // calc year of date arrays
        this.yearArr = this.getYearArray()
        this.wheelIndexYear = this.yearArr.findIndex((element) => element === this.currentYear) + 1
        this.pickerValYear = `${this.currentYear}`
        this.currenTranslateYear = 0 - (this.wheelIndexYear - 3) * this.itemHeight

        // calc month of date
        let currentMonth = this.currentDate.getMonth() + 1
        let monthVal = `${currentMonth}`
        if (currentMonth < 10) {
          monthVal = `0${currentMonth}`
        }
        this.wheelIndexMonth = this.monthArr.findIndex((element) => element === monthVal) + 1
        this.pickerValMonth = monthVal
        this.currenTranslateYMonth = 0 - (this.wheelIndexMonth - 3) * this.itemHeight

        // calc day of date
        let currentDay = this.currentDate.getDate()
        let dayVal = `${currentDay}`
        if (currentDay < 10) {
          dayVal = `0${currentDay}`
        }
        this.dayArr = this.getDayArray(this.currentYear, monthVal)
        this.wheelIndexDay = this.dayArr.findIndex((element) => element === dayVal) + 1
        this.pickerValDay = dayVal
        this.currenTranslateYDay = 0 - (this.wheelIndexDay - 3) * this.itemHeight
      },
      getDayArray (year, month) {
        if (month === '02') {
          if (this.isLeapYear(year)) {
            return this.autoGeneratorDateStrArray(29)
          }
          else {
            return this.autoGeneratorDateStrArray(28)
          }
        }
        else if (this.firstMonths.includes(month)) {
          return this.autoGeneratorDateStrArray(31)
        }
        else {
          return this.autoGeneratorDateStrArray(30)
        }
      },
      isLeapYear (y) {
        if (y % 4 === 0 && y % 100 > 0) {
          return true
        }
        else if (y % 400 === 0) {
          return true
        }
        else {
          return false
        }
      },
      getYearArray () {
        let endYear = this.currentYear + 30
        let diffCount = (endYear - this.startYear) + 1
        return this.autoGeneratorArray(diffCount, this.startYear)
      },
      autoGeneratorDateStrArray (length) {
        return Array.from({length: length}, (v, i) => {
          let d = i + 1
          if (d < 10) {
            return `0${d}`
          }
          else {
            return `${d}`
          }
        })
      },
      autoGeneratorArray (length, start = 0) {
        if (start === 0) {
          return Array.from({length: length}, (v, i) => i + 1)
        }
        else {
          return Array.from({length: length}, (v, i) => start + i)
        }
      },
      confirmFunc () {
        this.choosedVal = `${this.pickerValYear}-${this.pickerValMonth}-${this.pickerValDay}`
        this.valFunc(this.choosedVal)
      },
      handlerYear (obj) {
        this.handler(obj, 'yearArr', 'wheelIndexYear', 'currenTranslateYear', 'pickerValYear')
      },
      handlerMonth (obj) {
        this.handler(obj, 'monthArr', 'wheelIndexMonth', 'currenTranslateYMonth', 'pickerValMonth')
      },
      handlerDay (obj) {
        this.handler(obj, 'dayArr', 'wheelIndexDay', 'currenTranslateYDay', 'pickerValDay')
      },
      handler (obj, pickItemArrName, pickItemIndexName, currenTranslateYName, pickValName) {
        let diff = 0
        if (obj.distance.y > (this.itemHeight / 2)) {
          diff = obj.distance.y / this.itemHeight
        }
        let cindex = Math.round(diff)
        const totalMin = (this[pickItemArrName].length - 3) * this.itemHeight
        const me = this
        if (obj.direction === 'up') {
          this[currenTranslateYName] = this[currenTranslateYName] - obj.distance.y
          setTimeout(function () {
            me[pickItemIndexName] += cindex
            me[currenTranslateYName] = 0 - ((me[pickItemIndexName] - 3) * me.itemHeight)
            if (me[currenTranslateYName] < -totalMin) {
              me[currenTranslateYName] = -totalMin
              me[pickItemIndexName] = me[pickItemArrName].length
            }
          }, obj.duration)
        }

        if (obj.direction === 'down') {
          me[currenTranslateYName] = me[currenTranslateYName] + obj.distance.y
          setTimeout(function () {
            me[pickItemIndexName] -= cindex
            me[currenTranslateYName] = me[currenTranslateYName] - obj.distance.y + cindex * me.itemHeight
            if (me[currenTranslateYName] > me.itemHeight * 2) {
              me[currenTranslateYName] = me.itemHeight * 2
              me[pickItemIndexName] = 1
            }
          }, obj.duration)
        }
        setTimeout(function () {
          me[pickValName] = me[pickItemArrName][me[pickItemIndexName] - 1]
        }, obj.duration)
        // change feb month days index
        if (this.pickerValMonth === '02') {
          if (this.isLeapYear(this.pickerValYear)) {
            if (this.dayArr.length !== 29) {
              this.dayArr = this.autoGeneratorDateStrArray(29)
            }
          }
          else {
            let dayPickVal = Number(this.pickerValDay)
            if (this.dayArr.length !== 28) {
              if (dayPickVal > 28) {
                this.wheelIndexDay = 28
                this.currenTranslateYDay = 0 - (26 * this.itemHeight)
                this.pickerValDay = `${28}`
              }
              this.dayArr = this.autoGeneratorDateStrArray(28)
            }
          }
        }
      }
    }
  }

</script>

<style type="text/css",scoped>
  @import url("./picker.css");
</style>
