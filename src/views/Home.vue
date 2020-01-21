<template>
  <div class="home">
    <h1>Desired Schedule Generator</h1>
    <div>
      <VueCtkDateTimePicker
        v-model="selectedRange" v-bind:label="label"
        v-bind:format="dateFormat" v-bind:formatted="dateFormatted" v-bind:range="true"
      />
    </div>
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
  dateFormat:String = 'YYYY-MM-DD'
  dateFormatted:String = 'll'
  label:String = 'Select date range'
  selectedRange:DatepickerParams = { start: '2000-01-01', end: '2000-01-01' }

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
}
</script>
