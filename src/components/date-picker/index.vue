<template>
  <div>
    <div class="date-picker">
      <div class="header clearfix">
        <span class="prev fl" @click="prev()">&lt;</span>
        <span v-if="!dateList.length">{{nowDate}}</span>
        <span class="next fr" @click="next()">&gt;</span>
      </div>
      <div class="date-body">
        <ul class="clearfix">
          <li class="redColor">一</li>
          <li>二</li>
          <li>三</li>
          <li>四</li>
          <li>五</li>
          <li>六</li>
          <li class="redColor">日</li>
        </ul>
        <ul class="clearfix">
          <li
            v-for="(item, index) in dateArr"
            :key="index"
            :class="{disabled: item.disabled, active:active(item), redColor:index%7 === 0 || index% 7 === 6}"
            @click="dateClick(item)"
          >{{item.day}}</li>
        </ul>
      </div>
    </div>
    <div class="resultWarp">
      <span v-for="(item, index) in dateListTest()" :key="index">
        {{item}}<span v-if="index !== dateListTest().length - 1">,</span>
      </span>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    date: {
      type: String,
      default: '1992-07-25'
    },
    dateList: {
      type: Array,
      default () {
        return []
      }
    },
    isCheckbox: {
      type: Boolean,
      default: true
    },
    maxLen: {
      type: Number,
      default: 5
    }
  },
  data () {
    return {
      dateArr: [
      ],
      nowDate: this.date,
      json: {}
    }
  },
  created () {
    this.dateArr = this._ret(this.splitDate[0], this.splitDate[1])
  },
  methods: {
    active (item) {
      if (this.isCheckbox) {
        let day = this.nowDate.substring(0, 8) + (item.day < 10 ? '0' + item.day : item.day)
        return item.month === 'this' && this.dateListTest().indexOf(day) >= 0
      } else {
        return item.month === 'this' && item.day === +this.splitDate[2]
      }
    },
    _getFirstDayDate (year, month) {
      let oDate = new Date(year, month, 1)
      return oDate.getDay()
    },
    _getCountDays (month) {
      let oDate = new Date()
      oDate.setMonth(month, 0)
      return oDate.getDate()
    },
    _ret (year, month) {
      let ret = []
      let prevMonthDay = this._getCountDays(month - 1)
      let thisMonthDay = this._getCountDays(month)
      let firstDate = this._getFirstDayDate(year, month - 1)
      if (firstDate === 0) {
        firstDate = 7
      }
      if (firstDate === 1) {
        for (var i = prevMonthDay - 6; i <= prevMonthDay; i++) {
          ret.push({
            day: i,
            disabled: true,
            month: 'prev'
          })
        }
      } else {
        for (var b = prevMonthDay - firstDate + 2; b <= prevMonthDay; b++) {
          ret.push({
            day: b,
            disabled: true,
            month: 'prev'
          })
        }
      }
      for (var j = 1; j <= thisMonthDay; j++) {
        ret.push({
          day: j,
          disabled: false,
          month: 'this'
        })
      }
      let retLen = ret.length
      for (var z = 1; z <= 42 - retLen; z++) {
        ret.push({
          day: z,
          disabled: true,
          month: 'next'
        })
      }
      return ret
    },
    prev () {
      let splitDateArr = this.nowDate.split('-')
      let splitMonth = +splitDateArr[1]
      let splitYear = +splitDateArr[0]
      splitMonth--
      if (splitMonth < 10 && splitMonth !== 0) {
        splitMonth = '0' + splitMonth
      }
      if (splitMonth === 0) {
        splitYear--
        splitMonth = 12
      }
      splitDateArr[1] = splitMonth
      splitDateArr[0] = splitYear
      this.nowDate = splitDateArr.join('-')
      this.dateArr = this._ret(this.splitDate[0], this.splitDate[1])
    },
    next () {
      let splitDateArr = this.nowDate.split('-')
      let splitMonth = +splitDateArr[1]
      let splitYear = +splitDateArr[0]
      splitMonth++
      if (splitMonth < 10 && splitMonth !== 0) {
        splitMonth = '0' + splitMonth
      }
      if (splitMonth === 13) {
        splitYear++
        splitMonth = 1
      }
      splitDateArr[1] = splitMonth
      splitDateArr[0] = splitYear
      this.nowDate = splitDateArr.join('-')
      this.dateArr = this._ret(this.splitDate[0], this.splitDate[1])
    },
    dateClick (item) {
      let splitDateArr = this.nowDate.split('-')
      splitDateArr[2] = item.day
      if (splitDateArr[2] < 10) {
        splitDateArr[2] = '0' + splitDateArr[2]
      }
      if (item.month === 'prev') {
        splitDateArr[1] = +splitDateArr[1]
        splitDateArr[1]--
        if (splitDateArr[1] < 10 && splitDateArr[1] !== 0) {
          splitDateArr[1] = '0' + splitDateArr[1]
        }
        if (splitDateArr[1] === 0) {
          splitDateArr[0]--
          splitDateArr[1] = 12
        }
      }
      if (item.month === 'next') {
        splitDateArr[1] = +splitDateArr[1]
        splitDateArr[1]++
        if (splitDateArr[1] < 10) {
          splitDateArr[1] = '0' + splitDateArr[1]
        }
        if (splitDateArr[1] === 13) {
          splitDateArr[0]++
          splitDateArr[1] = 1
        }
      }
      this.nowDate = splitDateArr.join('-')
      if (this.isCheckbox) {
        if (this.json[splitDateArr.join('-')]) {
          delete this.json[splitDateArr.join('-')]
        } else {
          if (this.dateListTest().length >= this.maxLen) {
            return
          }
          this.json[splitDateArr.join('-')] = 1
        }
        this.$emit('click', this.dateListTest)
      } else {
        this.$emit('click', this.nowDate)
      }
      console.log(this.dateListTest(), this.nowDate)
      this.dateArr = this._ret(this.splitDate[0], this.splitDate[1])
    },
    dateListTest () {
      let ret = []
      for (var key in this.json) {
        ret.push(key)
      }
      return ret
    }
  },
  computed: {
    splitDate () {
      return this.nowDate.split('-')
    }
  }
}
</script>

<style scoped lang="scss">
  .date-picker {
    border: 1px solid #97a8be;
    width: 300px;
    margin: 150px auto 0;
    .header {
      padding: 0px 10px;
      line-height: 50px;
      font-size: 25px;
      border-bottom: 1px solid #97a8be;
      text-align: center;
      .prev, .next {
        color: #97a8be;
        cursor: pointer;
        &:hover {
          color: #008800;
        }
      }
    }
    .date-body {
      ul {
        &:nth-child(2) {
          li {
            cursor: pointer;
          }
        }
        li {
          float: left;
          width: percentage(1 / 7);
          text-align: center;
          padding: 10px 0px;
          &.disabled {
            color: gray;
          }
          &.active {
            background: #fb0;
            color: #fff;
          }
          @at-root {
            .redColor {
              color: red;
            }
            .resultWarp {
              margin-top: 20px;
              text-align: center;
            }
          }
        }
      }
    }
  }
</style>
