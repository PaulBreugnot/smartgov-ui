<template>
	<l-rectangle
		ref="tile"
		v-bind:bounds="bounds"
		v-on:click="handleClick"
		v-bind:weight="0"
		fill-color="red"
		color="red"
		/>
</template>

<script lang="coffee">
	import {LRectangle} from "vue2-leaflet"

	export default
		
		components:
			"l-rectangle": LRectangle

		props:
			bounds:
				type: Array
				required: true
			arcs:
				type: Array
				required: true
			pollutionPeek:
				type: Number
				required: true
			pollutant:
				type: String
				required: true

		data: () ->
			pollution: 0
			weight: 0

		watch:
			pollutionPeek: (newVal, oldVal) ->
				this.updateColor()

		methods:
			updateColor: () ->
				this.$refs["tile"].mapObject.setStyle({"fillOpacity": this.pollutionColor()})

			pollutionColor: () ->
				if this.pollutionPeek > 0
					alpha = this.pollution / this.pollutionPeek
					return alpha
				return 0

			pollutionMean: () ->
				pollutionSum = 0
				self = this
				for arc in this.arcs
					do (arc) ->
						if Number(arc.pollution[self.pollutant])
							pollutionSum += Number(arc.pollution[self.pollutant])

				return pollutionSum
			
			handleClick: () ->
				this.$emit('click')
				###
				newWeight = 0
				if this.weight == 0
					newWeight = 1

				this.weight = newWeight
				this.$refs["tile"].mapObject.setStyle({"weight": this.weight})
				###

		mounted: () ->
			self = this

			handlePollutionMean = (pollution) ->
					self.$emit('update:pollution-peek', pollution)
					self.pollution = pollution
					self.updateColor()

			this.$watch(
				this.pollutionMean,
				(newVal, oldVal) ->
					self.$emit('update:pollution-peek', newVal)
					self.pollution = newVal
					self.updateColor()
				)

			this.$nextTick(() ->
				handlePollutionMean(self.pollutionMean())
				)

</script>

