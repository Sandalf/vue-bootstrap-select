<template>
  <div id="app">
    <h2>Array of strings</h2>
    <VSelect v-model="selectedName"  :options="names"/>
    <h2>Array of objects</h2>
    <VSelect v-model="selectedContinent" :options="contients"/>
  </div>
</template>

<script>
import VSelect from "@/vue-bootstrap-select";

export default {
  name: "App",
  components: {
    VSelect
  },
  data() {
    return {
      names: [
        "John Doe",
        "Olive Yew",
        "Aida Bugg",
        "Teri Dactyl",
        "Paige Turner"
      ],
      contients: [
        { value: 0, text: "Africa" },
        { value: 1, text: "America" },
        { value: 2, text: "Asia" },
        { value: 3, text: "Europe" },
        { value: 4, text: "Oceania" }
      ],
      selectedName: null,
      selectedContinent: null
    };
  }
};
</script>


<style lang="scss">
#app {
  font-family: "Roboto", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  padding: 2em 1em;
}
</style>
