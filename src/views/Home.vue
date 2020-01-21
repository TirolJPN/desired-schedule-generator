<template>
  <div class="home">
    <h1>Desired Schedule Generator</h1>
    <p v-if="!areStartAndEndingTimesValid" class="error-message">{{errorTimeMessage}}</p>
    <div class="picker">
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
        v-bind:format="'hh:mm a'" v-bind:formatted="'hh:mm a'" v-bind:minute-interval="10"
      />
      <VueCtkDateTimePicker
        id="ending-time-picker" v-bind:label="'Select ending time'"
        v-model="endingTime" v-bind:no-label="true" v-bind:only-time="true"
        v-bind:format="'hh:mm a'" v-bind:formatted="'hh:mm a'" v-bind:minute-interval="10"
      />
    </div>
    <br><br><br>
    <!-- v-for of ExcludedDate components -->
    <div v-if="isSelectedDate">
      <ExcludedDate v-bind:enabledDates="enabledDates"/>
      <p>{{enabledDates}}</p>
      <button class="add-exlclude-date-button">
        <i class="fas fa-plus"></i>
        Add Exclude Date
      </button>
    </div>
    <div v-else>
      <p class="error-message">Please fill correct range</p>
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
  selectedRange:DatepickerParams = { start: '', end: '' }
  startTime:string = ''
  endingTime:string = ''
  idList:number[] = []
  errorTimeMessage:string = ''

  mounted () {
    let dt = new Date()
    let y = dt.getFullYear()
    let m = ('00' + (dt.getMonth() + 1)).slice(-2)
    let d = ('00' + dt.getDate()).slice(-2)
    this.selectedRange.start = y + '-' + m + '-' + d
    this.selectedRange.end = y + '-' + m + '-' + d
  }

  /**
   * computed
   */
  get isSelectedDate () {
    const checkValue = (s: string) => {
      return s !== '' && s !== null
    }
    return checkValue(this.selectedRange.start) && checkValue(this.selectedRange.end)
  }

  get enabledDates () {
    let result: string[] = []
    let day = new Date(this.selectedRange.start)
    let endDay = new Date(this.selectedRange.end)
    while (day.getTime() <= endDay.getTime()) {
      const tmpElm:string = day.getFullYear() + '-' + ('00' + day.getMonth() + 1).slice(-2) + '-' + ('00' + day.getDate()).slice(-2)
      result.push(tmpElm)
      day.setDate(day.getDate() + 1)
      const tmp:string = day.getFullYear() + '-' + ('00' + day.getMonth() + 1).slice(-2) + '-' + ('00' + day.getDate()).slice(-2)
      day = new Date(tmp)
    }
    return result
  }

  get areStartAndEndingTimesValid () {
    const checkValue = (s: string) => {
      return s !== '' && s !== null
    }
    const getTimeNumber = (s: string) => {
      const tmp = s.split(' ')
      const h = Number(tmp[0].split(':')[0])
      const m = Number(tmp[0].split(':')[1])
      const ap = tmp[1] === '午前' ? 0 : 1
      return h * 60 + m + ap * 12 * 60
    }
    if (!checkValue(this.startTime) || !checkValue(this.endingTime)) {
      this.errorTimeMessage = 'Fill start and ending time'
      return false
    } else if (getTimeNumber(this.startTime) < getTimeNumber(this.endingTime)) {
      this.errorTimeMessage = ''
      return true
    } else {
      this.errorTimeMessage = 'Incorrect start and ending time'
      return false
    }
  }
}
</script>
