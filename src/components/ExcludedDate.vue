<template>
<div>
  <p v-if="!isSelectedDate" class="error-message">select date</p>
  <p v-if="!areStartAndEndingTimesValid" class="error-message">{{errorTimeMessage}}</p>
  <div class="excluded-date-picker">
    <VueCtkDateTimePicker
      v-model="ExcludedDate.selectedDay" v-bind:enabledDates="enabledDates" v-bind:format="'YYYY-MM-DD'" v-bind:formatted="'ll'" v-bind:onlyDate="true"
    />
    <VueCtkDateTimePicker
      id="start-time-picker" v-bind:label="'Select start time'"
      v-model="ExcludedDate.startTime" v-bind:no-label="true" v-bind:only-time="true"
      v-bind:format="'HH:mm'" v-bind:formatted="'HH:mm'" v-bind:minute-interval="10"
    />
    <VueCtkDateTimePicker
      id="ending-time-picker" v-bind:label="'Select ending time'"
      v-model="ExcludedDate.endingTime" v-bind:no-label="true" v-bind:only-time="true"
      v-bind:format="'HH:mm'" v-bind:formatted="'HH:mm'" v-bind:minute-interval="10"
    />
  </div>
</div>
</template>

<script lang="ts">
import { Component, Prop, Vue } from 'vue-property-decorator'
import 'vue-ctk-date-time-picker/dist/vue-ctk-date-time-picker.css'
import '@/assets/stylesheets/excludedDate.css'
const VueCtkDateTimePicker = require('vue-ctk-date-time-picker')

interface ExcludedDateParams{
  id: number,
  selectedDay:string,
  startTime:string,
  endingTime:string,
}

@Component({
  components: {
    VueCtkDateTimePicker
  }
})

export default class VueCtkDateTime extends Vue {
  @Prop({ default: [] })
  enabledDates!: string[]
  @Prop({ default: () => {} })
  ExcludedDate!:ExcludedDateParams

  /**
   * data
   */
  errorTimeMessage:string = ''

  get isSelectedDate () {
    const checkValue = (s: string) => {
      return s !== '' && s !== null
    }
    return checkValue(this.ExcludedDate.selectedDay)
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
    if (!checkValue(this.ExcludedDate.startTime) || !checkValue(this.ExcludedDate.endingTime)) {
      this.errorTimeMessage = 'Fill start and ending time'
      return false
    } else if (getTimeNumber(this.ExcludedDate.startTime) < getTimeNumber(this.ExcludedDate.endingTime)) {
      this.errorTimeMessage = ''
      return true
    } else {
      this.errorTimeMessage = 'Incorrect start and ending time'
      return false
    }
  }
}
</script>
