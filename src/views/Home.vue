<template>
  <div class="home">
    <h1>Desired schedule</h1>
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
    <pre>{{scheduleOutput}}</pre>
    <pre>{{result}}</pre>
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

interface ExtendedDesiredDateParams{
  timeNumber: number
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

  /**
   * computed
   */
  get scheduleOutput () {
    const sortDesiredDates = () => {
      const sortDesiredDates = this.desiredDates.map(elm => {
        let day = new Date(elm.selectedDay)
        let dayNumber: number = Math.floor(new Date(elm.selectedDay).getTime() / (1000 * 60 * 60 * 24))
        let obj = {
          id: elm.id,
          selectedDay: elm.selectedDay,
          startTime: elm.startTime,
          endingTime: elm.endingTime,
          dateNum: dayNumber
        }
        return obj
      })
      return sortDesiredDates
    }

    interface ExtendedDesiredDateParams extends DesiredDateParams {
      dateNum: number
    }

    let sortedDesiredDates: ExtendedDesiredDateParams[] = sortDesiredDates().sort((a, b) => {
      if (a.dateNum < b.dateNum) return -1
      if (a.dateNum > b.dateNum) return 1
      return 0
    }).sort((a, b) => {
      if (a.startTime.length < b.startTime.length) return -1
      if (a.startTime.length > b.startTime.length) return 1
      return 0
    }).sort((a, b) => {
      if (a.endingTime.length < b.endingTime.length) return -1
      if (a.endingTime.length > b.endingTime.length) return 1
      return 0
    })

    let result: string = ''
    let isNeededContinue = true
    let oldDayNumber = 0
    for (let elm of sortedDesiredDates) {
      let day = new Date(elm.selectedDay)
      let count:number = sortedDesiredDates.filter((x) => { return x.dateNum === elm.dateNum }).length
      if (count === 1) {
        result += ('\n' + day.getFullYear() + '-' + ('00' + (day.getMonth() + 1)).slice(-2) + '-' + ('00' + day.getDate()).slice(-2) + ' ')
        if (elm.startTime !== '' && elm.startTime !== null && elm.endingTime !== '' && elm.endingTime !== null) {
          result += (elm.startTime + '~' + elm.endingTime)
        } else {
          result += '終日'
        }
      } else {
        if (oldDayNumber !== elm.dateNum) {
          oldDayNumber = elm.dateNum
          isNeededContinue = true
          result += ('\n' + day.getFullYear() + '-' + ('00' + (day.getMonth() + 1)).slice(-2) + '-' + ('00' + day.getDate()).slice(-2) + ' ')
          if (elm.startTime !== '' && elm.startTime !== null && elm.endingTime !== '' && elm.endingTime !== null) {
            result += (elm.startTime + '~' + elm.endingTime)
          } else {
            isNeededContinue = false
            result += '終日'
          }
        } else if (isNeededContinue) {
          result += (', ' + elm.startTime + '~' + elm.endingTime)
        } else {
          continue
        }
      }
    }
    return result
  }
}
</script>
