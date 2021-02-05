<template>
  <div id="app">
    <Landing />
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue'
import Landing from '@/pages/Landing.vue'

export default defineComponent({
  components: {
    Landing
  }
})
</script>

<style>
#app {
  height: 100vh;
  width: 100%;
}
</style>
