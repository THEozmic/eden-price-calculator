<template>
  <div id="app">
    <Calculator :pricing="pricing" />
  </div>
</template>

<script>
import Calculator from "./components/Calculator.vue";

export default {
  name: "app",
  components: {
    Calculator
  }
};
</script>

<style>
:root {
  --tertiary: #e5f5ee;
  --secondary: #ffeccd;
  --primary: #ff9d02;
  --black: #494949;
  --lighter-grey: #fafafa;
  --light-grey: #d6d6d6;
}

* {
  box-sizing: border-box;
}

#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  margin-top: 60px;
  padding: 16px;
}

body {
  background: var(--tertiary);
}
</style>
