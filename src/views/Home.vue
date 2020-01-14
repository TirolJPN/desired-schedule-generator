<template>
  <div class="home">
    <VueCtkDateTimePicker></VueCtkDateTimePicker>
  </div>
</template>

<script lang="ts">
// @ is an alias to /src
import { Component, Vue } from 'vue-property-decorator'
import VueCtkDateTimePicker from '@/components/VueCtkDateTimePicker.vue'

@Component({
  components: {
    VueCtkDateTimePicker
  }
})

export default class Home extends Vue {}
</script>
