<template>
  <div>
    <toolbar class="w3-center"></toolbar>
      <div class="w3-bar-block">
        <h2 class="w3-center">Settings</h2>
        <div class="w3-cell-row w3-padding">
          <p class="w3-cell">Scenario :</p>
          <div class="w3-dropdown-click">
            <button
              class="w3-button w3-black w3-theme-l2"
              v-on:click="showScenarios = true">
              <i class="fa fa-caret-down" aria-hidden="true"></i>
              {{selectedScenario}}
            </button>
            <div
              class="w3-dropdown-content w3-bar-block w3-border"
              v-bind:class="[showScenarios ? 'w3-show' : 'w3-hide']">
              <a class="w3-bar-item w3-button" v-on:click="selectScenario('Visualization')">Visualization</a>
              <a class="w3-bar-item w3-button" v-on:click="selectScenario('Delivery Rounds')">Delivery Rounds</a>
              <a class="w3-bar-item w3-button" v-on:click="selectScenario('Pollution')">Pollution</a>
              <a class="w3-bar-item w3-button" v-on:click="selectScenario('LEZ')">LEZ</a>
            </div>
          </div>
        </div>
        <button
          class="w3-button w3-block"
          v-on:click="settingsDisplay = 'simulation'"
          v-bind:class="[settingsDisplay == 'simulation' ? 'w3-theme' : 'w3-theme-l1']">Simulation</button>
        <form
          class="w3-container w3-padding-16 w3-animate-zoom"
          v-bind:class="[settingsDisplay == 'simulation' ? 'w3-show' : 'w3-hide']">
          <label>Agent Number :</label>
          <input class="w3-input" type="number" min="1">
          <label> Duration :</label>
          <duration-picker></duration-picker>
          <label> Tick Duration :</label>
          <input class="w3-input w3-border-0" type="range"></input>
        </form>
        <button
          class="w3-button w3-block"
          v-on:click="settingsDisplay = 'input'"
          v-bind:class="[settingsDisplay == 'input' ? 'w3-theme' : 'w3-theme-l1']">Input</button>
        <form
          class="w3-container w3-padding-16 w3-animate-zoom"
          v-bind:class="[settingsDisplay == 'input' ? 'w3-show' : 'w3-hide']">
          <label>Copert Table :</label>
          <input class="w3-input w3-border-0" type="file">
        </form>
        <button
          class="w3-button w3-block"
          v-on:click="settingsDisplay = 'population'"
          v-bind:class="[settingsDisplay == 'population' ? 'w3-theme' : 'w3-theme-l1']">Population</button>
        <form
          class="w3-container w3-padding-16 w3-animate-zoom"
          v-bind:class="[settingsDisplay == 'population' ? 'w3-show' : 'w3-hide']">
          <label>Establishments Profiles :</label>
          <input class="w3-input w3-border-0" type="file">
          <label>Population Profiles :</label>
          <input class="w3-input w3-border-0" type="file">
        </form>
        <button
          class="w3-button w3-block"
          v-on:click="settingsDisplay = 'policy'"
          v-bind:class="[settingsDisplay == 'policy' ? 'w3-theme' : 'w3-theme-l1']">Policy</button>
        <form
          class="w3-container w3-padding-16 w3-animate-zoom"
          v-bind:class="[settingsDisplay == 'policy' ? 'w3-show' : 'w3-hide']">
          <label>Policy Agents Number :</label>
          <input class="w3-input w3-border-0" type="number" min="1">
          <label>Utilities :</label>
          <input class="w3-input w3-border-0" type="file">
          <label>Epsilon :</label>
          <input class="w3-input w3-border-0" type="range">
          <label>Epochs :</label>
          <input class="w3-input w3-border-0" type="number" min="1">
        </form>
      </div>
  </div>
</template>

<script lang="coffee">
  import Toolbar from "./Toolbar"
  import DurationPicker from "./DurationPicker"

  export default
    components:
      "toolbar": Toolbar
      "duration-picker": DurationPicker

    data: () ->
      showScenarios: false
      selectedScenario: "Visualization"
      settingsDisplay: 'simulation'


    methods:
      selectScenario: (scenario) ->
        this.selectedScenario = scenario
        this.showScenarios = false

</script>

<style>

</style>
