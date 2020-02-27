<template>
  <transition name="modal" appear>
    <div class="modal modal-overlay" @click.self="$emit('close')">
      <div class="modal-window">
        <div class="modal-content">
          <slot/>
        </div>
        <h2>
          Desired scheduleは、空いた予定の一覧を示す文字列を簡単に取得できます
        </h2>
        <p>日付を入力することで、その日が空いた日であることを示します</p>
        <p>時間が空欄になっている日については終日空いているものとして扱います</p>
        <p>「copy to clipboard」をクリップボードにコピーできます</p>
        <button class="times-button" @click="$emit('close')" >
          <i class="fas fa-times"></i>
        </button>
      </div>
    </div>
  </transition>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator'
import '@/assets/stylesheets/about.css'

export default class Home extends Vue {}
</script>
