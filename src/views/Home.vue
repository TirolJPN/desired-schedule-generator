<template>
  <div class="home">
    <h1>Desired Schedule Generator</h1>
    <h2>Desired schedule</h2>
    <p v-if="!areSelectedDates" class="error-message">Fill correct date range</p>
    <p v-if="!areStartAndEndingTimesValid" class="error-message">{{errorTimeMessage}}</p>
    <div class="home-picker">
      <!-- to pick date range -->
      <VueCtkDateTimePicker
        id="date-range-picker"
        v-model="selectedRange" v-bind:label="'Select date range'"
        v-bind:format="'YYYY-MM-DD'" v-bind:formatted="'ll'" v-bind:range="true"
      />
      <!-- to pick time range -->
      <VueCtkDateTimePicker
        id="start-time-picker" v-bind:label="'Select start time'"
        v-model="startTime" v-bind:no-label="true" v-bind:only-time="true"
        v-bind:format="'hh:mm a'"  v-bind:formatted="'hh:mm a'" v-bind:minute-interval="10"
      />
      <VueCtkDateTimePicker
        id="ending-time-picker" v-bind:label="'Select ending time'"
        v-model="endingTime" v-bind:no-label="true" v-bind:only-time="true"
        v-bind:format="'hh:mm a'" v-bind:formatted="'hh:mm a'" v-bind:minute-interval="10"
      />
    </div>

    <!-- v-for of ExcludedDate components -->
    <div v-if="areSelectedDates && areStartAndEndingTimesValid">
      <h2>Not desired schedule</h2>
      <ExcludedDate
        v-for="(ExcludedDateElm) in excludedDates" :key="ExcludedDateElm.id"
        v-bind:ExcludedDate="ExcludedDateElm" v-bind:enabledDates="enabledDates"
      />
      <button class="add-exlclude-date-button" v-on:click="addExcludedDate">
        <i class="fas fa-plus"></i>
        Add Excluded Date
      </button>

      <h3>for debug</h3>
      <!-- <p>selectedRange:{{selectedRange}}</p>
      <p>enabledDates:{{enabledDates}}</p>
      <p>excludedDates:{{excludedDates}}</p> -->
      <pre>{{scheduleOutput}}</pre>
    </div>
  </div>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator'
import 'vue-ctk-date-time-picker/dist/vue-ctk-date-time-picker.css'
import '@/assets/stylesheets/home.css'
import ExcludedDate from '@/components/ExcludedDate.vue'

const VueCtkDateTimePicker = require('vue-ctk-date-time-picker')

interface DatepickerParams{
  start: string,
  end: string
}
interface ExcludedDateParams{
  id: number,
  selectedDay:string,
  startTime:string,
  endingTime:string,
}

@Component({
  components: {
    VueCtkDateTimePicker,
    ExcludedDate
  }
})

export default class Home extends Vue {
  /**
   * data
   */
  errorTimeMessage:string = ''
  selectedRange:DatepickerParams = { start: '', end: '' }
  startTime:string = ''
  endingTime:string = ''
  excludedDates:ExcludedDateParams[] = []

  mounted () {
    // let dt = new Date()
    // let y = dt.getFullYear()
    // let m = ('00' + (dt.getMonth() + 1)).slice(-2)
    // let d = ('00' + dt.getDate()).slice(-2)
    // this.selectedRange.start = y + '-' + m + '-' + d
    // this.selectedRange.end = y + '-' + m + '-' + d
  }

  /**
   * methods
   */
  addExcludedDate () {
    // Math.max.apply(null,gGpsData.map(function(o){return o.speed;}))
    let id = this.excludedDates.length === 0 ? 0 : Math.max.apply(null, this.excludedDates.map((elm) => { return elm.id }))
    // dummy
    this.excludedDates.push({
      id: id + 1,
      selectedDay: '',
      startTime: '',
      endingTime: ''
    })
  }

  checkStringEmptyNull = (s: string) => {
    return s !== '' && s !== null
  }

  getTimeNumber = (s: string) => {
    const tmp = s.split(' ')
    const h = Number(tmp[0].split(':')[0])
    const m = Number(tmp[0].split(':')[1])
    const ap = tmp[1] === '午前' ? 0 : 1
    return h * 60 + m + ap * 12 * 60
  }

  /**
   * computed
   */
  get areSelectedDates () {
    return this.checkStringEmptyNull(this.selectedRange.start) && this.checkStringEmptyNull(this.selectedRange.end)
  }

  get enabledDates () {
    let result: string[] = []
    let day = new Date(this.selectedRange.start)
    let endDay = new Date(this.selectedRange.end)
    while (day.getTime() <= endDay.getTime()) {
      result.push(day.getFullYear() + '-' + ('00' + (day.getMonth() + 1)).slice(-2) + '-' + ('00' + day.getDate()).slice(-2))
      day.setDate(day.getDate() + 1)
      day = new Date(day.getFullYear() + '-' + ('00' + (day.getMonth() + 1)).slice(-2) + '-' + ('00' + day.getDate()).slice(-2))
    }
    return result
  }

  get areStartAndEndingTimesValid () {
    if (!this.checkStringEmptyNull(this.startTime) || !this.checkStringEmptyNull(this.endingTime)) {
      this.errorTimeMessage = 'Fill start and ending time'
      return false
    } else if (this.getTimeNumber(this.startTime) < this.getTimeNumber(this.endingTime)) {
      this.errorTimeMessage = ''
      return true
    } else {
      this.errorTimeMessage = 'Incorrect start and ending time'
      return false
    }
  }

  get scheduleOutput () {
    const checkSchedule = (tmpOutputString:string, date:string) => {
      let excludedDates:ExcludedDateParams[] = this.excludedDates
      let day = new Date(date)
      let outputString: string = day.getFullYear() + '-' + ('00' + (day.getMonth() + 1)).slice(-2) + '-' + ('00' + day.getDate()).slice(-2)
      let isIncludedDate = true
      for (let excludedDate of excludedDates) {
        if (Math.floor(new Date(excludedDate.selectedDay).getTime() / (1000 * 60 * 60 * 24)) === Math.floor(day.getTime() / (1000 * 60 * 60 * 24))) {
          if (excludedDate.startTime !== '' && excludedDate.startTime !== null && excludedDate.endingTime !== '' && excludedDate.endingTime !== null) {
            // 時間情報の文字列生成
            outputString += '無理'
            isIncludedDate = true
          } else {
            isIncludedDate = false
          }
        }
      }
      return tmpOutputString + (isIncludedDate ? outputString + '\n' : '')
    }

    const enabledDates = this.enabledDates
    return enabledDates.reduce(checkSchedule, '')
  }
}
</script>
