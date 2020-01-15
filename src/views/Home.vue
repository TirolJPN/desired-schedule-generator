<template>
  <div class="home">
    <p>{{pairStartAndEndDays}}</p>
    <!-- <VueCtkDateTimePicker v-model="hoge"></VueCtkDateTimePicker> -->
    <VueCtkDateTimePicker v-model="pairStartAndEndDays" v-bind:range="true" v-bind:only-date="true" />
  </div>
</template>

<script lang="ts">
// @ is an alias to /src
import { Component, Vue } from 'vue-property-decorator'
import 'vue-ctk-date-time-picker/dist/vue-ctk-date-time-picker.css'
const VueCtkDateTimePicker = require('vue-ctk-date-time-picker')

@Component({
  components: {
    VueCtkDateTimePicker
  }
})

export default class Home extends Vue {
  /**
   * data
   * startDay: the first day in which you are free
   * endDay: the last day in which you are fere
   */
  pairStartAndEndDays:Object = {}
}
</script>
