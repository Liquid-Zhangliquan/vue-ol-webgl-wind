<template>
  <div id="app">
    <router-view />
  </div>
</template>

<script>
export default {
  name: 'app',
  mounted(){
    console.log('app')
  }
}
</script>

<style>
html,
body,
#app {
  width: 100%;
  height: 100%;
  padding: 0px;
  margin: 0px;
  overflow: hidden;
}
</style>
