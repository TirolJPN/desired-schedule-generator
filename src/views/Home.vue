<template>
  <div class="home">
    <!-- <p>{{pairStartAndEndDays}}</p> -->
    <VueCtkDateTime v-model="pairStartAndEndDays" v-bind:range="true" v-bind:only-date="true" />
  </div>
</template>

<script lang="ts">
// @ is an alias to /src
import { Component, Vue } from 'vue-property-decorator'
import VueCtkDateTime from '@/components/VueCtkDateTime.vue'

@Component({
  components: {
    VueCtkDateTime
  }
})

export default class Home extends Vue {
  /**
   * data
   * pairSrartAndEndDays: A pair of first and end candidate dates
   */
  pairStartAndEndDays:Object = {}
}
</script>
