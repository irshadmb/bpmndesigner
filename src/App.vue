<template>
  <AutomationPage/>
</template>

<script>
import AutomationPage from './components/Automation/AutomationPage.vue';

export default {
  name: 'App',
  components: {
    AutomationPage
  }
}
</script>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
html{
  font-family: "Helvetica Neue", Helvetica, Arial, sans-serif;
}
#app {
  font-family: "Helvetica Neue", Helvetica, Arial, sans-serif;
  font-size: 14px;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: left;
  color: #2c3e50;
  margin-left: -8px;
}
</style>
