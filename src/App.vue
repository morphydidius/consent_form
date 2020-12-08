<template lang="pug">
  div(id="app")
    FormCompo
</template>

<script>
import FormCompo from './components/FormCompo.vue'

export default {
  name: 'App',
  components: {
    FormCompo
  }
}
</script>

<style>
#app {
  font: 16px/24px 'Arial';
}
</style>
