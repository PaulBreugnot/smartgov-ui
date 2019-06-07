<template>
  <div>
  	<div class="w3-cell-row">
		<build-button class="run-button w3-cell w3-button w3-round-large w3-amber w3-tiny"/>
		<start-button v-bind:duration="simulationDuration" class="run-button w3-cell w3-button w3-round-large w3-green w3-tiny"/>
    	<button class="run-button w3-cell w3-button w3-round-large w3-yellow w3-tiny" disabled><i class="fas fa-pause"></i></button>
    	<button class="run-button w3-cell w3-button w3-round-large w3-red w3-tiny" disabled><i class="fas fa-stop"></i></button>
  	</div>
  </div>
</template>

<script lang="coffee">
	import BuildButton from "./controls/buildButton"
	import StartButton from "./controls/startButton"

	export default
		components:
			"build-button": BuildButton
			"start-button": StartButton

		props:
			["simulationDuration"]

</script>

<style>

.run-button {
  margin: 0px 4px;
}

</style>
