<template>
  <div class="home">
    <h1>Desired Schedule Generator</h1>
    <div>
      <!-- <p>{{selectedRange}}</p> -->
      <VueCtkDateTimePicker v-model="selectedRange" v-bind:format="dateFormat" v-bind:formatted="dateFormatted" v-bind:range="true"/>
    </div>
    <div v-if="isSelectedDate">
      <!-- v-for of ExcludedDate components -->
      <!-- <ExcludedDate/> -->
      <button class="add-exlclude-date-button">
        <i class="fas fa-plus"></i>
        Add Exclude Date
      </button>
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
}
</script>
