<template>
  <div id="app">
    <input-box></input-box>
    <area-list></area-list>
    <alert-box></alert-box>
  </div>
</template>

<script>
import AlertBox from "./components/alertBox.vue";
import AreaList from "./components/areaList.vue";
import inputBox from "./components/inputBox.vue";

export default {
  name: "App",
  components: {
    inputBox,
    AreaList,
    AlertBox,
  },
};
</script>

<style>
:root {
  --text-color: #fff;
  --button-color: #03a9f4;
  --body-color: #000;
  --box-color:#2c2c2e;
}
* {
  margin: 0;
  padding: 0;
}
body {
  background-color: var(--body-color);
  overflow-y: hidden;
  width: 200vh;
  height: 100vh;
}
body::-webkit-scrollbar {
  display: none;
}
#app {
  /* display: flex;
  justify-content: center;
  align-items: center; */
  flex-wrap: wrap;
  position: relative;
  width: 100%;
  height: 100%;
}
input[type="submit"] {
  width: 100px;
  height: 40px;
  background-color: var(--button-color);
  color: var(--text-color);
  border: 0;
  border-radius: 10px;
}
</style>
