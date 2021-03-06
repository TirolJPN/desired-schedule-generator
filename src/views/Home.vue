<template>
  <div class="home">
    <div class="title">
      <h1>
        Desired Schedule
        <button class="about-button" @click="openModal()" >
        <i class="far fa-question-circle"></i>
      </button>
      </h1>
      <About @close="closeModal()" v-if="modalFlag" />
    </div>
    <div class="copybox" v-tooltip="{ content: 'Copied!', trigger: 'click', autoHide: true }" @click="copyToClipboard()">
      <span class="box-title">copy to clipboard</span>
      <pre id="copy-target">{{scheduleOutput}}</pre>
    </div>
    <transition-group class="transition-parent" name="desired-date-list" tag="div">
      <div v-for="(desiredDate) in desiredDates" :key="desiredDate.id">
        <DesiredDate
          v-bind:desiredDate="desiredDate" @eventDeleteDesiredDate=deleteDesiredDate />
      </div>
    </transition-group>
    <button class="add-desired-date-button" v-on:click="addDesiredDate">
      <i class="fas fa-plus"></i>
      Add Desired Date
    </button>
  </div>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator'
import 'vue-ctk-date-time-picker/dist/vue-ctk-date-time-picker.css'
import '@/assets/stylesheets/home.css'
import DesiredDate from '@/components/DesiredDate.vue'
import About from '@/components/About.vue'

const VueCtkDateTimePicker = require('vue-ctk-date-time-picker')
const VTooltip = require('v-tooltip')

Vue.use(VTooltip)

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
    DesiredDate,
    About
  }
})

export default class Home extends Vue {
  /**
   * data
   */
  errorTimeMessage:string = ''
  desiredDates:DesiredDateParams[] = []
  modalFlag:boolean = false

  mounted () {
    let day = new Date()
    this.desiredDates.push({
      id: 1,
      selectedDay: ('\n' + day.getFullYear() + '-' + ('00' + (day.getMonth() + 1)).slice(-2) + '-' + ('00' + day.getDate()).slice(-2) + ' '),
      startTime: '',
      endingTime: ''
    })
    day.setDate(day.getDate() + 1)
    day = new Date(day.getFullYear() + '-' + ('00' + (day.getMonth() + 1)).slice(-2) + '-' + ('00' + day.getDate()).slice(-2))
    this.desiredDates.push({
      id: 2,
      selectedDay: ('\n' + day.getFullYear() + '-' + ('00' + (day.getMonth() + 1)).slice(-2) + '-' + ('00' + day.getDate()).slice(-2) + ' '),
      startTime: '09:00',
      endingTime: '12:00'
    })
    this.desiredDates.push({
      id: 3,
      selectedDay: ('\n' + day.getFullYear() + '-' + ('00' + (day.getMonth() + 1)).slice(-2) + '-' + ('00' + day.getDate()).slice(-2) + ' '),
      startTime: '15:00',
      endingTime: '18:00'
    })
  }

  /**
   * methods
   */
  openModal () {
    this.modalFlag = true
  }

  closeModal () {
    this.modalFlag = false
  }

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
  }

  copyToClipboard () {
    const copyText = this.$el.querySelector('#copy-target')!.textContent
    if (copyText === null) return
    navigator.clipboard
      .writeText(copyText)
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

    let sortedDesiredDates: ExtendedDesiredDateParams[] = sortDesiredDates()
      .sort((a, b) => a.dateNum - b.dateNum)
      .sort((a, b) => a.startTime.length - b.startTime.length)
      .sort((a, b) => a.endingTime.length - b.endingTime.length)

    let result: string = ''
    let isNeededContinue = true
    let oldDayNumber = 0
    for (let elm of sortedDesiredDates) {
      let day = new Date(elm.selectedDay)
      if (day.toString() === 'Invalid Date') {
        continue
      }
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
    return result.slice(1)
  }
}
</script>
