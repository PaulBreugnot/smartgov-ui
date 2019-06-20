<template>
	<div class="w3-container w3-margin-top w3-margin-bottom">
		<div class="w3-row-padding">
		<div class="w3-half">
			<input
				class="w3-check"
				type="checkbox"
				v-bind:checked="graph"
				v-on:change="$emit('update:graph',$event.target.checked)"
				/>
			<label>Graph</label>
		</div>
		<div class="w3-half">
			<input
				class="w3-check"
				type="checkbox"
				v-bind:checked="tiles"
				v-on:change="$emit('update:tiles',$event.target.checked)"
				/>
			<label>Tiles</label>
		</div>
		</div>
		<div class="w3-center w3-margin-top">
			<label>Tile size</label>
			<input
				class="w3-input"
				type="range"
				min="5"
				max="50"
				v-bind:value="tileWidthRatio"
				v-on:input="$emit('update:tile-width-ratio', Number($event.target.value))"
				/>
			<label>Pollutant</label>
			<pollutant-picker
				v-bind:pollutant="pollutant"
				v-on:update:pollutant="$emit('update:pollutant', $event)"
				/>
		</div>
	</div>
</template>

<script lang="coffee">
	import PollutantPicker from "../tools/PollutantPicker"

	export default
		components:
			"pollutant-picker": PollutantPicker

		props:
			graph:
				type: Boolean
				required: true
			tiles:
				type: Boolean
				required: true
			tileWidthRatio:
				type: Number
				required: true
			pollutant:
				type: String
				required: true

</script>
