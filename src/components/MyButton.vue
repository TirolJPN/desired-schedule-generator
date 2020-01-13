<template>
  <VueCtkDateTimePicker v-model="ts" v-bind:range="true" v-bind:only-date="true" />
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator'
import 'vue-ctk-date-time-picker/dist/vue-ctk-date-time-picker.css'

// interface HogeDatapicker{}
const VueCtkDateTimePicker = require('vue-ctk-date-time-picker')

@Component({
  components: {
    VueCtkDateTimePicker
  }
})
export default class Mybutton extends Vue {
  public ts = { 'start': '2018-04-09', 'end': '2018-04-10' }
}
</script>
