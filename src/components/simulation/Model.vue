<template>
	<div id="simulation-body" class="w3-theme-l4">
		<header id="body-header" class="w3-theme-d1">
			<div id="tabs-bar" class="w3-bar w3-theme-l4">
				<button
					class="tab-button w3-bar-item w3-button w3-hover-theme"
					v-bind:class="{'w3-theme-d1': currentTab == 'simulation'}">Simulation</button>
				<button
					class="tab-button w3-bar-item w3-button w3-hover-theme"
					v-bind:class="{'w3-theme-d1': currentTab == 'population'}">Population</button>
				<button
					class="tab-button w3-bar-item w3-button w3-hover-theme"
					v-bind:class="{'w3-theme-d1': currentTab == 'analysis'}">Analysis</button>
			</div>
		</header>
		<div id="body" class="w3-cell-row">
			<sidebar
			id="model-sidebar"
			class="w3-cell"
			v-bind:graph.sync="displaySettings.graph"
			v-bind:tiles.sync="displaySettings.tiles"
			v-bind:tile-width-ratio.sync="displaySettings.tileWidthRatio"
			v-bind:pollutant.sync="displaySettings.pollutant"
			v-on:update:graph="checkUpdate"
			>
			</sidebar>
			<div id="map-container" class="w3-cell w3-cell-top w3-border">
				<simulation-map
						v-bind:display-settings="displaySettings"	
						id="leaflet-map">
				</simulation-map>
			</div>
		</div>
	</div>
</template>

<script lang="coffee">
Stomp = require('stompjs')

import Sidebar from "./sidebar/Sidebar"
import SimulationMap from "./map/SimulationMap"

export default
	components:
		"sidebar": Sidebar
		"simulation-map": SimulationMap

	data: () ->
		currentTab: 'simulation'
		simulationDuration: null
		displaySettings:
			graph: true
			tiles: false
			establishments: true
			pollutant: "NOx"
			tileWidthRatio: 10
	
	methods:
		checkUpdate: (event) ->
			console.log event
	
</script>

<style>
/* #simulation-body {
	margin-left: 15%;
} */
#leaflet-map {
	position: relative;
	width: 100%;
	height: 100%;
}

#model-sidebar {
	width: 20%;
	padding: 10px 0px;
}

#map-container {
	width: 85%;
}

#simulation-body {
	height: 92%;
}

#body-header {
	height: 5%;
}

#body {
	height: 95%;
}

#tabs-bar {
	height: 100%;
}

.tab-button {
	height: 100%;
}
</style>
