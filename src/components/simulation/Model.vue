<template>
  <div class="w3-card w3-margin w3-theme-l4">
    <header class="w3-theme-d1">
      <h2 class="w3-center">Low Emission Zones</h2>
      <div class="w3-bar w3-theme-l4">
        <button
          class="w3-bar-item w3-button w3-hover-theme"
          v-bind:class="{'w3-theme-d1': currentTab == 'simulation'}">Simulation</button>
        <button
          class="w3-bar-item w3-button w3-hover-theme"
          v-bind:class="{'w3-theme-d1': currentTab == 'population'}">Population</button>
        <button
          class="w3-bar-item w3-button w3-hover-theme"
          v-bind:class="{'w3-theme-d1': currentTab == 'analysis'}">Analysis</button>
      </div>
    </header>
  <div class="w3-cell-row">
    <sidebar
		id="model-sidebar"
		class="w3-cell"
		>
	</sidebar>
    <div id="map-container" class="w3-cell w3-cell-top w3-border">
      <simulation-map id="leaflet-map"></simulation-map>
    </div>
  </div>
</div>
</template>

<script lang="coffee">
Stomp = require('stompjs')

import Sidebar from "./Sidebar"
import SimulationMap from "./map/SimulationMap"

export default
	components:
		"sidebar": Sidebar
		"simulation-map": SimulationMap

	data: () ->
		currentTab: 'simulation'
		simulationDuration: null
	
</script>

<style>
/* #simulation-body {
  margin-left: 15%;
} */
#leaflet-map {
  position: relative;
  width: 100%;
  height: 70vh;
}

#model-sidebar {
  width: 15%;
  padding: 10px 0px;
}

#map-container {
  width: 85%;
}
</style>
