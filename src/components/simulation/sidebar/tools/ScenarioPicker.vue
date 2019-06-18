<template>
	<div class="w3-dropdown-click w3-cell">
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
</template>

<script lang="coffee">

export default

	data: () ->
		showScenarios: false
		selectedScenario: "Visualization"

	methods:
		selectScenario: (scenario) ->
			this.selectedScenario = scenario
			this.showScenarios = false
</script>
