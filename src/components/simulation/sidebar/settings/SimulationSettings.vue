<template>
	<form class="w3-container w3-padding-16 w3-animate-zoom">
		<label> Duration :</label>
		<duration-picker
			v-bind:duration="simulationDuration"
			v-on:update:duration="$emit('update:simulation-duration', $event)"
		></duration-picker>
		<label> Tick Duration (s) :</label>
		<period-input
			v-bind:max="10"
			v-bind:init="tickDuration"
			v-on:update:period="$emit('update:tick-duration', $event)"/>
	</form>
</template>

<script lang="coffee">
import DurationPicker from "../tools/DurationPicker"
import PeriodInput from "../tools/PeriodInput"

export default

	props:
		simulationDuration:
			type: Number
			required: true

		tickDuration:
			type: Number
			required: true
	
	components:
		"duration-picker": DurationPicker
		"period-input": PeriodInput
	
	mounted: () ->
		console.log(this.tickDuration)

</script>
