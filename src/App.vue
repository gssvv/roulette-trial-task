<template>
  <div id="app">
    <roulette-component />
  </div>
</template>

<script>
import RouletteComponent from "@/components/RouletteComponent";

export default {
  name: "app",
  components: {
    RouletteComponent
  }
};
</script>

<style lang="sass">
#app
  font-family: "Avenir", Helvetica, Arial, sans-serif
</style>
