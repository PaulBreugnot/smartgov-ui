<template>
	<l-featuregroup>

		<l-featuregroup
			v-if="displaySettings.graph"
			ref="arcMap">
			<l-polyline
				v-if="nodes"
				v-for="(arc, id) in arcs"
				v-bind:ref="arc.id + '_' + displaySettings.pollutant"
				v-bind:lat-lngs="computedArcCoordinates[arc.id]"
				v-on:click="selectArc(arc, displaySettings.pollutant)"
				color="red"
				/>
				<!--:lat-lngs="arcCoordinates(arc)"-->
				<!--:color="pollutantColor(arc, pollutant)"-->
			</l-polyline>
			<l-popup v-if="selectedArc">
				<p>Id: {{selectedArc.id}}</p>
				<p>Pollution rate : {{selectedArc.pollution[displaySettings.pollutant]}} g/s</p>
			</l-popup>
			
		</l-featuregroup>

		<tile-map
			ref="tileMap"
			v-bind:pollutant="displaySettings.pollutant"
			v-if="displaySettings.tiles"
			v-bind:arcs="arcs"
			v-bind:nodes="nodes"
			v-bind:tile-width-ratio="displaySettings.tileWidthRatio"
			v-bind:l-map="lMap"
			v-on:select-arcs="selectArcs($event)"
			/>

	</l-featuregroup>
</template>

<script lang="coffee">
	import { LFeatureGroup, LPolyline, LPopup } from "vue2-leaflet"
	import TileMap from "./tileMap/TileMap"

	export default

		components:
			"l-featuregroup": LFeatureGroup
			"l-polyline": LPolyline
			"l-popup": LPopup
			"tile-map": TileMap

		props:
			lMap:
				type: Object
				required: true

			displaySettings:
				type: Object
				required: true

		data: () ->
			nodes: { }
			arcs: { }
			selectedArc: null # Arc selected on click
			selectedArcs: [] # Arcs selected from a tile
			pollutants: ["NO2", "NOx", "NH3", "VOC", "PM", "CH4", "CO", "FC"]
			pollutionPeeks:
				"NOx": 0
				"NO2": 0
				"NH3": 0
				"VOC": 0
				"PM": 0
				"CH4": 0
				"CO": 0
				"FC": 0

		watch:
			"displaySettings.graph": (newVal, oldVal) ->
				this.$nextTick(() ->
					if newVal
						this.updateArcsPollution(Object.values(this.arcs))
				)

			"displaySettings.pollutant": (newVal, oldVal) ->
				this.$nextTick(() ->
					if newVal
						this.updateArcsPollution(Object.values(this.arcs))
				)


		computed:
			computedArcCoordinates: () ->
				console.log("Computing arc coordinates...")
				coordinates = {}
				coordinates[id] = this.arcCoordinates(arc) for id, arc of this.arcs
				return coordinates

		methods:
			nodeCoordinates: (node) ->
				return L.latLng(node.position[0], node.position[1])

			arcCoordinates: (arc) ->
				startNode = this.nodes[arc.startNode]
				targetNode = this.nodes[arc.targetNode]
				return [this.nodeCoordinates(startNode), this.nodeCoordinates(targetNode)]

			arcMidCoordinates: (arc) ->
				startNode = this.nodes[arc.startNode]
				targetNode = this.nodes[arc.targetNode]
				return L.latLng((startNode.position[0] + targetNode.position[0]) / 2, (startNode.position[1] + targetNode.position[1]) / 2)

			selectArc: (arc, layerRef) ->
				this.selectedArc = arc
				console.log("Arc selected on layer #{layerRef} :")
				console.log(arc)
				this.$refs.arcMap.mapObject.openPopup(this.arcMidCoordinates(arc))
				###
				unless layerRef == "None"
					this.$refs[layerRef][0].mapObject.openPopup(this.arcMidCoordinates(arc))
				else
					this.$refs.None.mapObject.openPopup(this.arcMidCoordinates(arc))
					###

			setUpNodes: (nodes) ->
				this.nodes = nodes

			updateArc: (arc) ->
				arcToUpdate = this.arcs[arc.id]
				if arcToUpdate
					# The arc is already initialized
					for key, value of arc
						arcToUpdate[key] = value

				else
					# this is a new arc
					this.$set(this.arcs, arc.id, arc)

			updateArcsPollution: (pollutedArcs) ->
				self = this
				pollutant = this.displaySettings.pollutant
				for pollutedArc in pollutedArcs
					do (pollutedArc) ->
						arcToUpdate = self.arcs[pollutedArc.id]
						arcToUpdate.pollution[pollutant] = pollutedArc.pollution[pollutant]
						alpha = 0
						if self.displaySettings.graph
							if self.pollutionPeeks[pollutant] > 0
								alpha = arcToUpdate.pollution[pollutant] / self.pollutionPeeks[pollutant]
							self.$refs[pollutedArc.id + '_' + pollutant][0].mapObject.setStyle({"opacity": alpha})

			# Used to highlight arcs when a box is selected, only on the None layer
			selectArcs: (selectedArcs) ->
				console.log selectedArcs
				self = this
				setArcsColor = (arcs, color) ->
					for arc in arcs
						do (arc) ->
							console.log self.$refs
							console.log self.$refs[arc.id + '_' + self.displaySettings.pollutant]
							self.$refs[arc.id + '_' + self.displaySettings.pollutant][0].mapObject.setStyle({"color": color})

				setArcsColor(this.selectedArcs, "red")

				if this.selectedArcs != selectedArcs
					this.selectedArcs = selectedArcs
					setArcsColor(this.selectedArcs, "green")
				else
					this.selectedArcs = []

			fetchArcs: () ->
				url = process.env.VUE_APP_ARCS

				self = this
				fetch(url)
				.catch((error) ->
					console.log(error)
					)
				.then((response) ->
					response.json()
					)
				.then((json) ->
					self.updateArc(arc) for arc in json

					console.log("Arcs :")
					console.log(json)
					console.log(self.arcs)
				)

			fetchPollutionPeeks: () ->
				url = process.env.VUE_APP_POLLUTION_PEEKS

				self = this
				fetch(url)
				.catch((error) ->
					console.log(error)
					)
				.then((response) ->
					response.json()
					)
				.then((json) ->
					self.pollutionPeeks[pollutant] = Number(value) for pollutant, value of json

					console.log("Pollution peeks :")
					console.log(json)
					console.log(self.pollutionPeeks)
				)

			fetchPollution: () ->
				url = process.env.VUE_APP_POLLUTION

				self = this
				fetch(url)
				.catch((error) ->
					console.log(error)
					)
				.then((response) ->
					response.json()
					)
				.then((json) ->
					self.updateArcsPollution(json)
				)


			setUpWebSocket: (stompClient) ->
				self = this
				stompClient.subscribe('/simulation/arcs', (message) ->
						updatedArcs = JSON.parse(message.body)
						self.updateArc(arc) for arc in updatedArcs

						console.log(self.arcs)
					)

				stompClient.subscribe('/simulation/pollution_peeks', (message) ->
						# console.log("processing pollution peeks")
						updatedPollutionPeeks = JSON.parse(message.body)
						self.pollutionPeeks[pollutant] = Number(value) for pollutant, value of updatedPollutionPeeks

					)

				stompClient.subscribe('/simulation/pollution', (message) ->
						# console.log("processing polluted arcs")
						pollutedArcs = JSON.parse(message.body)
						self.updateArcsPollution(pollutedArcs)
						# self.updateArcPollution(pollutedArc) for pollutedArc in pollutedArcs
					)

</script>
