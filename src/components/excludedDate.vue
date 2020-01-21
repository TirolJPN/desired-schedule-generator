<template>
<div>
  <VueCtkDateTimePicker
    v-model="selectedDay" v-bind:enabledDates="enabledDates" v-bind:format="dateFormat" v-bind:formatted="dateFormatted" v-bind:onlyDate="true"
  />
</div>
</template>

<script lang="ts">
import { Component, Prop, Vue } from 'vue-property-decorator'
import 'vue-ctk-date-time-picker/dist/vue-ctk-date-time-picker.css'
const VueCtkDateTimePicker = require('vue-ctk-date-time-picker')

@Component({
  components: {
    VueCtkDateTimePicker
  }
})

export default class VueCtkDateTime extends Vue {
  @Prop({ default: [] })
  enabledDates!: string[]

  dateFormat:String = 'YYYY-MM-DD'
  dateFormatted:String = 'll'
  selectedDay:String = ''
}
</script>
