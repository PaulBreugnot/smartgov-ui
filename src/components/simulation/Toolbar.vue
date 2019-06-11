<template>
	<div>
		<div class="w3-cell-row">
		<start-button
			v-bind:duration="simulationDuration"
			v-bind:disabled="!(enabled && startEnabled)"
			v-on:sg-start="handleSimulationStart"
			v-on:sg-stop="handleSimulationStop"
			class="run-button w3-cell w3-button w3-round-large w3-green w3-tiny"
			/>
		<pause-button
			v-bind:paused="paused"
			v-bind:disabled="!(enabled && pauseEnabled)"
			v-on:sg-paused="stepEnabled = true;paused=true"
			v-on:sg-resumed="stepEnabled = false;paused=false"
			class="run-button w3-cell w3-button w3-round-large w3-yellow w3-tiny"/>

		<step-button
			v-bind:disabled="!(enabled && stepEnabled)"
			class="run-button w3-cell w3-button w3-round-large w3-yellow w3-tiny"/>
		
		<stop-button
			v-bind:disabled="!(enabled && stopEnabled)"
			class="run-button w3-cell w3-button w3-round-large w3-red w3-tiny"/>
		</div>
	</div>
</template>

<script lang="coffee">
	import BuildButton from "./controls/buildButton"
	import StartButton from "./controls/startButton"
	import PauseButton from "./controls/pauseButton"
	import StepButton from "./controls/stepButton"
	import StopButton from "./controls/stopButton"

	export default
		components:
			"build-button": BuildButton
			"start-button": StartButton
			"pause-button": PauseButton
			"step-button": StepButton
			"stop-button": StopButton

		props:
			["simulationDuration", "enabled"]
		
		data: () ->
			"startEnabled": true
			"pauseEnabled": false
			"stepEnabled": false
			"stopEnabled": false
			"paused": false

		methods:
			handleSimulationStart: () ->
				this.startEnabled = false
				this.pauseEnabled = true
				this.stopEnabled = true

			handleSimulationStop: () ->
				this.startEnabled = true
				this.pauseEnabled = false
				this.stepEnabled = false
				this.stopEnabled = false
				this.paused = false

</script>

<style>

.run-button {
	margin: 0px 4px;
}

</style>
