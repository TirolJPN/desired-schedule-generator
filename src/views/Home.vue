<template>
  <div class="home">
    <h1>Desired Schedule Generator</h1>
    <h2>Pick up  your desired schedule date and time range</h2>
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
      <h2>Pick up  your not desired schedule date and time range</h2>
      <ExcludedDate
        v-for="(ExcludedDateElm) in ExcludedDates" :key="ExcludedDateElm.id"
        v-bind:ExcludedDate="ExcludedDateElm" v-bind:enabledDates="enabledDates"
      />
      <button class="add-exlclude-date-button" v-on:click="addExcludedDate">
        <i class="fas fa-plus"></i>
        Add Excluded Date
      </button>

      <h3>for debug</h3>
      <p>enabledDates:{{enabledDates}}</p>
      <p>ExcludedDates:{{ExcludedDates}}</p>
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
  ExcludedDates:ExcludedDateParams[] = []

  mounted () {
    // let dt = new Date()
    // let y = dt.getFullYear()
    // let m = ('00' + (dt.getMonth() + 1)).slice(-2)
    // let d = ('00' + dt.getDate()).slice(-2)
    // this.selectedRange.start = y + '-' + m + '-' + d
    // this.selectedRange.end = y + '-' + m + '-' + d
    // this.idList.push(0)
  }

  /**
   * methods
   */
  addExcludedDate () {
    // Math.max.apply(null,gGpsData.map(function(o){return o.speed;}))
    let id = this.ExcludedDates.length === 0 ? 0 : Math.max.apply(null, this.ExcludedDates.map((elm) => { return elm.id }))
    // dummy
    this.ExcludedDates.push({
      id: id + 1,
      selectedDay: '',
      startTime: '',
      endingTime: ''
    })
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
    if (!checkValue(this.startTime) || !checkValue(this.endingTime)) {
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
}
</script>
