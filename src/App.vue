<template>
  <div id="app">
    <MyDesk/>
  </div>
</template>

<script>
import MyDesk from './components/MyDesk'

export default {
  name: 'App',
  components: {
    MyDesk
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
