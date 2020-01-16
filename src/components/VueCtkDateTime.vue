<template>
<div>
  <p>{{ts}}</p>
  <VueCtkDateTimePicker v-model="ts" v-bind:range="true" v-bind:only-date="true" />
</div>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator'
import 'vue-ctk-date-time-picker/dist/vue-ctk-date-time-picker.css'
const VueCtkDateTimePicker = require('vue-ctk-date-time-picker')

interface HogeDatapicker{
  start: string,
  end: string
}

@Component({
  components: {
    VueCtkDateTimePicker
  }
})

export default class VueCtkDateTime extends Vue {
  ts:HogeDatapicker = { start: '2019-04-09', end: '2019-04-30' }
}
</script>
