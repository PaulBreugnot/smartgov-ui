<template>
  <div id="app">
      <div class="w3-bar w3-theme-d1">
        <h1 class="w3-bar-item">SmartGov UI</h1>
        <h3 class="w3-bar-item w3-right">OpenSource Multi-agent Simulator for Urban Policies Optimization</h3>
      </div>
      <div class="w3-bar w3-theme-l4">
        <button
          class="w3-bar-item w3-button w3-hover-theme"
          v-bind:class="{'w3-theme-d1': currentTab == 'model'}">Model</button>
        <button
          class="w3-bar-item w3-button w3-hover-theme"
          v-bind:class="{'w3-theme-d1': currentTab == 'documentation'}">Documentation</button>
      </div>
    <component v-bind:is="currentTab"></component>
  </div>
</template>

<script lang="coffee">
import Model  from "./components/simulation/Model"

export default

  name: 'app'

  components:
    "Model": Model

  data: () ->
    currentTab: "model"


</script>

<style>
h3 {
  font-style: italic;
}
</style>
