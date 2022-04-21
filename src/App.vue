<template>
  <div id="app">
    <h1><span>D</span>Plan</h1>
    <h3>Your best plans, tasks and goals application</h3>
    <PlanBoard :tabs="tabs" />
  </div>
</template>

<script lang="ts">
import Vue from "vue";
import PlanBoard from "./components/PlanBoard.vue";

export default Vue.extend({
  name: "App",
  components: {
    PlanBoard,
  },
  data: () => ({
    tabs: ["All", "To do", "Done"],
  }),
});
</script>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #35495e;
}

h1 {
  font-size: 4em;
  margin: 0px;
  line-height: 1em;

  span {
    color: #41b883;
  }

  @media only screen and (max-width: 600px) {
    & {
      font-size: 2em;
    }
  }
}

h3 {
  font-size: 1.5em;
  margin: 0px 0px 16px 0px;
  line-height: 1em;

  span {
    color: #41b883;
  }

  @media only screen and (max-width: 600px) {
    & {
      font-size: 0.75em;
    }
  }
}
</style>
