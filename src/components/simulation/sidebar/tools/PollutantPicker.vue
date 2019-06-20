<template>
	<div class="w3-dropdown-click">
		<button
			class="w3-button w3-black w3-theme-l2"
			v-on:click="showPollutants = true">
			<i class="fa fa-caret-down" aria-hidden="true"></i>
			{{pollutant}}
		</button>
		<div
			class="w3-dropdown-content w3-bar-block w3-border"
			v-bind:class="[showPollutants ? 'w3-show' : 'w3-hide']">
				<a
					class="w3-bar-item w3-button"
					v-for="pollutant in pollutants"
					v-on:click="selectPollutant(pollutant)">
					{{ pollutant }}
				</a>
		</div>
	</div>
</template>

<script lang="coffee">

export default

	props:
		pollutant:
			type: String
			required: true

	data: () ->
		showPollutants: false
		pollutants: ["NO2", "NOx", "NH3", "VOC", "PM", "CH4", "CO", "FC"]

	methods:
		selectPollutant: (pollutant) ->
			this.showPollutants = false
			this.$emit('update:pollutant', pollutant)
</script>
