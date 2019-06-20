<template>
	<l-rectangle
		ref="tile"
		v-bind:bounds="bounds"
		v-on:click="$emit('click')"
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

		data: () ->
			pollution: 0

		watch:
			pollutionPeek: (oldVal, newVal) ->
				this.updateColor()

			arcs: (oldVal, newVal) ->
				console.log "hey"

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
				for arc in this.arcs
					do (arc) ->
						if Number(arc.pollution.NOx)
							pollutionSum += Number(arc.pollution.NOx)

				return pollutionSum

		mounted: () ->
			self = this
			this.$watch(
				this.pollutionMean,
				(oldVal, newVal) ->
					self.$emit('update:pollution-peek', newVal)
					self.pollution = newVal
					self.updateColor()
				)
</script>

