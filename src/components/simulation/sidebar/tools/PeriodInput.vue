<template>
	<div class="w3-row">
		<div class="w3-col period-slider">
		<input
			class="w3-input"
			type="range"
			v-model.number="period"
			v-on:input="$emit('update:period', period)"
			v-bind:max="max"
			v-bind:min="min"/>
		</div>
		<div class="w3-col period-number-input">
		<input
			class="w3-input"
			type="number"
			v-bind:min="min"
			v-bind:max="max"
			v-model.number="period"
			v-on:input="$emit('update:period', period)"
			/>
		</input>
		</div>
	</div>
</template>

<script lang="coffee">

	export default

		props:
			"max":
				type: Number
				required: true
			
			"init":
				type: Number
				required: false

			"min":
				type:Number
				required: false
				default: 1
		
		data: () ->
			"period": 1

		methods:
			computePeriod: () ->
				this.max - this.rawInput + 1

			handleNumericInput: (newValue) ->
				this.rawInput = this.max - newValue + 1
				this.$emit('update:refresh-period', newValue)

		mounted: () ->
			if this.init
				console.log "init : #{this.init}"
				this.period = this.init
			else
				this.period = Math.floor(this.max / 2)
			this.$emit('update:period', this.period)

</script>

<style>
.period-slider {
	width: 60%;
	padding-right: 4px;
}

.period-number-input {
	width: 40%
}
</style>
