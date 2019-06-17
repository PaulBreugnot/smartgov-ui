<template>
	<div>
		<toolbar
			class="w3-center"
			v-bind:enabled="toolbarEnabled"
			v-bind:simulation-duration="simulationDuration">
		</toolbar>
			<div class="w3-bar-block">
				<h2 class="w3-center">Settings</h2>
				<div class="w3-cell-row w3-padding">
					<scenario-picker/>
					<build-button
						v-on:sg-built="toolbarEnabled = true"
						class="run-button w3-cell w3-button w3-round-large w3-amber w3-tiny"/>

				</div>
				<button
					class="w3-button w3-block"
					v-on:click="settingsDisplay = 'simulation'"
					v-bind:class="[settingsDisplay == 'simulation' ? 'w3-theme' : 'w3-theme-l1']">Simulation</button>
				<simulation-settings
					v-bind:simulation-duration.sync="simulationDuration"
					v-bind:class="[settingsDisplay == 'simulation' ? 'w3-show' : 'w3-hide']"/>

				<button
					class="w3-button w3-block"
					v-on:click="settingsDisplay = 'input'"
					v-bind:class="[settingsDisplay == 'input' ? 'w3-theme' : 'w3-theme-l1']">Input</button>
				<input-settings
					v-bind:class="[settingsDisplay == 'input' ? 'w3-show' : 'w3-hide']"/>

				<button
					class="w3-button w3-block"
					v-on:click="settingsDisplay = 'population'"
					v-bind:class="[settingsDisplay == 'population' ? 'w3-theme' : 'w3-theme-l1']">Population</button>
				<population-settings
					v-bind:class="[settingsDisplay == 'population' ? 'w3-show' : 'w3-hide']"/>

				<button
					class="w3-button w3-block"
					v-on:click="settingsDisplay = 'policy'"
					v-bind:class="[settingsDisplay == 'policy' ? 'w3-theme' : 'w3-theme-l1']">Policy</button>
				<policy-settings
					v-bind:class="[settingsDisplay == 'policy' ? 'w3-show' : 'w3-hide']"/>
			</div>
	</div>
</template>

<script lang="coffee">
import BuildButton from "../controls/buildButton"
import Toolbar from "./Toolbar"
import ScenarioPicker from "./ScenarioPicker"

import SimulationSettings from "./SimulationSettings"
import InputSettings from "./InputSettings"
import PopulationSettings from "./PopulationSettings"
import PolicySettings from "./PolicySettings"

export default
	components:
		"build-button": BuildButton
		"toolbar": Toolbar
		"scenario-picker": ScenarioPicker
		"simulation-settings": SimulationSettings
		"input-settings": InputSettings
		"population-settings": PopulationSettings
		"policy-settings": PolicySettings

	data: () ->
		toolbarEnabled: false
		settingsDisplay: 'simulation'
		simulationDuration: 100


</script>

<style>

</style>
