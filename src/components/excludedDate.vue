<template>
<div>
  <VueCtkDateTimePicker v-model="selectedDay" v-bind:format="dateFormat" v-bind:formatted="dateFormatted"/>
</div>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator'
import 'vue-ctk-date-time-picker/dist/vue-ctk-date-time-picker.css'
const VueCtkDateTimePicker = require('vue-ctk-date-time-picker')

interface selectedDay{
  day: String
}

@Component({
  components: {
    VueCtkDateTimePicker
  }
})

export default class VueCtkDateTime extends Vue {
  dateFormat:String = 'YYYY-MM-DD'
  dateFormatted:String = 'll'
  selectedDay:selectedDay = { day: '' }
}
</script>
