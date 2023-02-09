<template>
  <div id="app">
    <keep-alive>
      <ToDo> </ToDo>
    </keep-alive>
  </div>
</template>

<script>
import ToDo from "./components/tode.vue";
export default {
  name: "App",
  components: {
    ToDo,
  },
};
</script>

<style lang="less">
* {
  padding: 0;
  margin: 0;
  border: 0;
  box-sizing: border-box;
}
body {
  background-color: #f5f5f5;
  font: 14px "Helvetica Neue", Helvetica, Arial, sans-serif;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  // text-align: center;
  // color: #2c3e50;
  // margin-top: 60px;
}
</style>
