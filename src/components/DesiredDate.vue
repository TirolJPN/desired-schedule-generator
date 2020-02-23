<template>
<div>
  <p v-if="!isSelectedDate" class="error-message">日付を入力してください</p>
  <p v-if="!areStartAndEndingTimesValid" class="error-message">時間の値を正しく設定してください</p>
  <div class="desired-date-picker">
    <VueCtkDateTimePicker
      v-model="desiredDate.selectedDay" v-bind:format="'YYYY-MM-DD'" v-bind:formatted="'ll'" v-bind:onlyDate="true"
    />
    <VueCtkDateTimePicker
      id="start-time-picker" v-bind:label="'Select start time'"
      v-model="desiredDate.startTime" v-bind:no-label="true" v-bind:only-time="true"
      v-bind:format="'HH:mm'" v-bind:formatted="'HH:mm'" v-bind:minute-interval="10"
    />
    <VueCtkDateTimePicker
      id="ending-time-picker" v-bind:label="'Select ending time'"
      v-model="desiredDate.endingTime" v-bind:no-label="true" v-bind:only-time="true"
      v-bind:format="'HH:mm'" v-bind:formatted="'HH:mm'" v-bind:minute-interval="10"
    />
    <button class="delete-desired-date-button" v-on:click="commitDeleteDesiredDate(desiredDate.id)">
      <i class="fas fa-trash-alt"></i>
    </button>
  </div>
</div>
</template>

<script lang="ts">
import { Component, Prop, Vue, Emit, Watch } from 'vue-property-decorator'
import 'vue-ctk-date-time-picker/dist/vue-ctk-date-time-picker.css'
import '@/assets/stylesheets/desiredDate.css'
const VueCtkDateTimePicker = require('vue-ctk-date-time-picker')

interface desiredDateParams{
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
  @Prop({ default: () => {} })
  desiredDate!:desiredDateParams

  errorTimeMessage:string = ''

  @Emit('eventDeleteDesiredDate')
  commitDeleteDesiredDate (id: number): void {}

  get isSelectedDate () {
    const checkValue = (s: string) => {
      return s !== '' && s !== null
    }
    return checkValue(this.desiredDate.selectedDay)
  }

  get areStartAndEndingTimesValid () {
    const checkValue = (s: string) => {
      return s !== '' && s !== null
    }
    const getTimeNumber = (s: string) => {
      const h = Number(s.split(':')[0])
      const m = Number(s.split(':')[1])
      return h * 60 + m
    }
    if (getTimeNumber(this.desiredDate.startTime) >= getTimeNumber(this.desiredDate.endingTime)) {
      return false
    }
    return true
  }

  @Watch('desiredDate.startTime')
  checkStartNull (old: desiredDateParams, newHoge: desiredDateParams) {
    if (this.desiredDate.startTime === null) {
      this.desiredDate.startTime = ''
    }
  }

  @Watch('desiredDate.endingTime')
  checkEndNull (old: desiredDateParams, newHoge: desiredDateParams) {
    if (this.desiredDate.endingTime === null) {
      this.desiredDate.endingTime = ''
    }
  }
}
</script>
