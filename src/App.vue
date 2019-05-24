<template>
  <div id="app">
    <div class="w3-container w3-theme-d1">
      <h1>SmartGov UI</h1>
    </div>
    <div class="w3-bar w3-theme-d2">
      <button class="w3-bar-item w3-button w3-hover-theme">Simulation</button>
      <button class="w3-bar-item w3-button w3-hover-theme">Analysis</button>
      <button class="w3-bar-item w3-button w3-hover-theme">Documentation</button>
    </div>
    <component v-bind:is="currentTab"></component>
  </div>
</template>

<script lang="coffee">
import Simulation  from "./components/simulation/Simulation"

export default

  name: 'app'

  components:
    "simulation": Simulation

  data: () ->
    currentTab: "simulation"


</script>

<style>



</style>
