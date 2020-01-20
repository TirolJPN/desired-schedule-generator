<template>
  <div class="home">
    <h1>Desired Schedule Generator</h1>
    <p>{{selectedRange}}</p>
    <VueCtkDateTimePicker v-model="selectedRange" v-bind:format="dateFormat" v-bind:formatted="ll" v-bind:range="true"/>
  </div>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator'
import 'vue-ctk-date-time-picker/dist/vue-ctk-date-time-picker.css'
const VueCtkDateTimePicker = require('vue-ctk-date-time-picker')

interface DatepickerParams{
  start: string,
  end: string
}

interface ExclodedDateList{
  dataList: [DatepickerParams]
}

@Component({
  components: {
    VueCtkDateTimePicker
  }
})

export default class Home extends Vue {
  dateFormat:String = 'YYYY-MM-DD'
  selectedRange:DatepickerParams = { start: '2019-04-09', end: '2019-04-30' }
}
</script>
