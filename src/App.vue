<template>
  <div id="app">
    <router-view />
  </div>
</template>

<script>
import SideBar from 'com/side-bar'
export default {
  data () {
    return {
      name: ''
    }
  },
  components: {
    SideBar
  }
}
</script>

<style lang="scss" scoped>
</style>
