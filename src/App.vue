<template>
  <div id="app">
    <transition name="fade" mode="out-in">
      <router-view :key="key"></router-view>
    </transition>
  </div>
</template>
<script>
export default {
  name: 'App',
  computed: {
    key () {
      return this.$route.name !== undefined ? this.$route.name + +this.$route.query._t : this.$route
    }
  }
}
</script>

<style>

#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
