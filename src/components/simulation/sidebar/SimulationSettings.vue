<template>
	<form class="w3-container w3-padding-16 w3-animate-zoom">
		<label> Duration :</label>
		<duration-picker
			v-bind:duration="simulationDuration"
			v-on:update:duration="$emit('update:simulation-duration', $event)"
		></duration-picker>
		<label> Tick Duration :</label>
		<input class="w3-input w3-border-0" type="range"></input>
	</form>
</template>

<script lang="coffee">
import DurationPicker from "./DurationPicker"

export default

	props:
		["simulationDuration"]
	
	components:
		"duration-picker": DurationPicker

</script>
