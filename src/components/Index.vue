<template lang="pug">
  q-layout
    .toolbar(slot="header")
      q-toolbar-title(:padding='0') Quasar Wheelpicker demo
    .layout-view.bg-gray
      .list.item-delimiter
        .item.bg-white(v-for="item in listData",@click="clickItem(item.type)")
          .item-content
            | {{ item.name }}
            div.pull-right
              span(v-if="item.type==0") {{basicPickVal}}
              span(v-if="item.type==1") {{maxPickVal}}
              span(v-if="item.type==2") {{cityPickVal}}
              span(v-if="item.type==3") {{datePickVal}}
              i.ft-20.pull-right keyboard_arrow_right
    picker(:isOpen="basicPickOpen", :valFunc="basicPicker", :pickerItems="basicArr")
    picker(:isOpen="maxPickOpen", :valFunc="maxPicker", :pickerItems="maxPickArr")
    city-picker(:isOpen="cityPickOpen", :valFunc="cityPicker")
    date-picker(:isOpen="datePickOpen", :valFunc="datePicker")
</template>

<script>
import Picker from './picker'
import CityPicker from './picker/citypicker.vue'
import DatePicker from './picker/datepicker.vue'
export default {
  data () {
    return {
      listData: [{
        name: 'basic wheel picker',
        type: 0
      }, {
        name: 'three group picker(max group count)',
        type: 1
      }, {
        name: 'wheel citypicker',
        type: 2
      }, {
        name: 'wheel datepicker',
        type: 3
      }],
      basicPickOpen: false,
      basicPickVal: '',
      basicArr: [[1, 2, 3, 4, 5, 6, 7, 8]],
      maxPickOpen: false,
      maxPickVal: '',
      maxPickArr: [[1, 2, 3], [4, 6], [7, 8]],
      cityPickVal: '',
      cityPickOpen: false,
      datePickVal: '',
      datePickOpen: false,
      pickValNameArr: ['basicPickVal', 'maxPickVal', 'cityPickVal', 'datePickVal']
    }
  },
  components: {
    Picker,
    CityPicker,
    DatePicker
  },
  methods: {
    clickItem (type) {
      switch (type) {
        case 0:
          this.basicPickOpen = !this.basicPickOpen
          this.resetPickVal('basicPickVal')
          break
        case 1:
          this.maxPickOpen = !this.maxPickOpen
          this.resetPickVal('maxPickVal')
          break
        case 2:
          this.cityPickOpen = !this.cityPickOpen
          this.resetPickVal('cityPickVal')
          break
        case 3:
          this.datePickOpen = !this.datePickOpen
          this.resetPickVal('datePickVal')
          break
        default:
          break
      }
    },
    resetPickVal (choosedPickValName) {
      const me = this
      this.pickValNameArr.map((val) => {
        if (val !== choosedPickValName) {
          me[val] = ''
        }
      })
    },
    basicPicker (val) {
      this.commonToggle(val, 'basicPickOpen', 'basicPickVal')
    },
    maxPicker (val) {
      this.commonToggle(val, 'maxPickOpen', 'maxPickVal')
    },
    cityPicker (val) {
      this.commonToggle(val, 'cityPickOpen', 'cityPickVal')
    },
    datePicker (val) {
      this.commonToggle(val, 'datePickOpen', 'datePickVal')
    },
    commonToggle (val, pickOpenName, pickValName) {
      if (val === 'close') {
        this[pickOpenName] = false
      }
      else {
        this[pickOpenName] = !this[pickOpenName]
        if (val !== '') {
          this[pickValName] = val
        }
      }
    }
  }
}
</script>

<style type="text/css">
  .ft-20 {
    font-size: 20px;
  }
  .bg-gray {
    background-color: #eee;
  }

  .bg-white {
    background-color: #fff;
  }

  .list {
    border:0px;
  }

  .modal-content {
    width: 100%;
  }

  .padding {
    padding: 10px 10px;
  }

  .border-bottom-line {
    border: 1px solid #ddd;
  }

</style>
