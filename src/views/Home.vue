<template>
  <div class="home">
    <h2>Desired schedule</h2>
    <transition-group class="transition-parent" name="desired-date-list" tag="div">
      <div
        v-for="(desiredDate) in desiredDates"
        :key="desiredDate.id"
      >
        <DesiredDate
          v-bind:desiredDate="desiredDate" @eventDeleteDesiredDate=deleteDesiredDate
        />
      </div>
    </transition-group>
    <button class="add-desired-date-button" v-on:click="addDesiredDate">
      <i class="fas fa-plus"></i>
      Add Desired Date
    </button>
    <h3>for debug</h3>
    <!-- <p>selectedRange:{{selectedRange}}</p> -->
    <!-- <p>enabledDates:{{enabledDates}}</p> -->
    <p>desiredDates:{{desiredDates}}</p>
    <pre>{{scheduleOutput}}</pre>
  </div>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator'
import 'vue-ctk-date-time-picker/dist/vue-ctk-date-time-picker.css'
import '@/assets/stylesheets/home.css'
import DesiredDate from '@/components/DesiredDate.vue'

const VueCtkDateTimePicker = require('vue-ctk-date-time-picker')

interface DesiredDateParams{
  id: number,
  selectedDay:string,
  startTime:string,
  endingTime:string,
}

@Component({
  components: {
    VueCtkDateTimePicker,
    DesiredDate
  }
})

export default class Home extends Vue {
  /**
   * data
   */
  errorTimeMessage:string = ''
  desiredDates:DesiredDateParams[] = []

  mounted () {
    this.desiredDates.push({
      id: 1,
      selectedDay: '',
      startTime: '',
      endingTime: ''
    })
  }

  /**
   * methods
   */
  addDesiredDate () {
    // Math.max.apply(null,gGpsData.map(function(o){return o.speed;}))
    let id = this.desiredDates.length === 0 ? 0 : Math.max.apply(null, this.desiredDates.map((elm) => { return elm.id }))
    let latestDesiredDate:DesiredDateParams = this.desiredDates.find(element => element.id === id)!
    this.desiredDates.push({
      id: id + 1,
      selectedDay: latestDesiredDate.selectedDay,
      startTime: latestDesiredDate.startTime,
      endingTime: latestDesiredDate.endingTime
    })
  }

  deleteDesiredDate (id: number) {
    if (this.desiredDates.some(e => e.id === id) && this.desiredDates.length > 1) {
      const index = this.desiredDates.findIndex((e) => e.id === id)
      this.desiredDates.splice(index, 1)
    }
    console.log('delete ' + id)
  }

  getTimeNumber = (s: string) => {
    const h = Number(s.split(':')[0])
    const m = Number(s.split(':')[1])
    return h * 60 + m
  }

  /**
   * computed
   */
  get scheduleOutput () {
    /**
     * accumulator: コールバックの返り値の累積
     * currentValue: 現在処理中の要素の値
     */
    const checkSchedule = (accumulator:string, currentValue:string) => {
      let desiredDates:DesiredDateParams[] = this.desiredDates
      let day = new Date(currentValue)
      let outputString: string = day.getFullYear() + '-' + ('00' + (day.getMonth() + 1)).slice(-2) + '-' + ('00' + day.getDate()).slice(-2)
      let isIncludedDate = true
      for (let desiredDate of desiredDates) {
        if (Math.floor(new Date(desiredDate.selectedDay).getTime() / (1000 * 60 * 60 * 24)) === Math.floor(day.getTime() / (1000 * 60 * 60 * 24))) {
          if (desiredDate.startTime !== '' && desiredDate.startTime !== null && desiredDate.endingTime !== '' && desiredDate.endingTime !== null) {
            // 時間情報の文字列生成
            outputString += '無理'
            isIncludedDate = true
          } else {
            isIncludedDate = false
          }
        }
      }
      return accumulator + (isIncludedDate ? outputString + '\n' : '')
    }

    // const enabledDates = this.enabledDates
    // return enabledDates.reduce(checkSchedule, '')
    return ''
  }
}
</script>
