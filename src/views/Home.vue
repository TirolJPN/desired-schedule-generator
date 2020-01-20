<template>
  <div class="home">
    <h1>Desired Schedule Generator</h1>
    <div>
      <p>{{selectedRange}}</p>
      <VueCtkDateTimePicker v-model="selectedRange" v-bind:format="dateFormat" v-bind:formatted="dateFormatted" v-bind:range="true"/>
    </div>
    <div>
      <!-- v-for of excludedDate components -->
      <button class="add-exlclude-date-button">
        <i class="fas fa-plus"></i>
        Add Exclude Date
      </button>
    </div>
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
  dateFormatted:String = 'll'
  selectedRange:DatepickerParams = { start: '2019-04-09', end: '2019-04-30' }
}
</script>

<style>
button:focus {
 outline: 0;
}

.add-exlclude-date-button {
  border: none;
  color: white;
  padding: 16px 32px;
  text-align: center;
  text-decoration: none;
  display: inline-block;
  font-size: 16px;
  margin: 4px 2px;
  -webkit-transition-duration: 0.4s; /* Safari */
  transition-duration: 0.4s;
  cursor: pointer;
  width: 100%;
  background-color: white;
  color: #888;
}
.add-exlclude-date-button:hover {
  background-color: #888;
  color: white;
}
</style>
