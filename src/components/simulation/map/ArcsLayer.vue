<template>
	<l-featuregroup>
		<l-featuregroup
			v-for="pollutant in pollutants"
			:ref="pollutant">
			<l-polyline
				v-if="nodes"
				v-for="(arc, id) in arcs"
				:lat-lngs="arcCoordinates(arc)"
				:color="pollutantColor(arc, pollutant)"
				v-on:click="selectArc(arc, pollutant)">
			</l-polyline>
			<l-popup v-if="selectedArc">
				<p>Id: {{selectedArc.id}}</p>
				<p>Pollution rate : {{selectedArc.pollution[pollutant]}} g/s</p>
			</l-popup>
		</l-featuregroup>
		<l-featuregroup
			ref="None">
			<l-polyline
				v-if="nodes"
				v-for="(arc, id) in arcs"
				:lat-lngs="arcCoordinates(arc)"
				v-on:click="selectArc(arc, 'None')"
				color="green"/>
			<l-popup v-if="selectedArc">
				<p>Id: {{selectedArc.id}}</p>
				<p v-for="(rate, pollutant) in selectedArc.pollution">{{pollutant}} : {{rate}} g/s</p>
			</l-popup>
		</l-featuregroup>
	</l-featuregroup>
</template>

<script lang="coffee">
	import { LFeatureGroup, LPolyline, LPopup } from "vue2-leaflet"
	hsv2rgb = (h,s,v) ->
		f = (n) ->
			k = (n+h/60)%6
			return v-v*s*Math.max(Math.min(k,4-k,1),0);
		return [f(5),f(3),f(1)];

	export default

		components:
			"l-featuregroup": LFeatureGroup
			"l-polyline": LPolyline
			"l-popup": LPopup

		props:
			["simulation-map"]

		data: () ->
			nodes: null
			arcs: { }
			selectedArc: null
			pollutants: ["NO2", "NOx", "NH3", "VOC", "PM", "CH4", "CO", "FC"]
			pollutionPeeks: {}

		methods:
			nodeCoordinates: (node) ->
				return L.latLng(node.position[1], node.position[0])

			arcCoordinates: (arc) ->
				startNode = this.nodes[arc.startNode]
				targetNode = this.nodes[arc.targetNode]
				return [this.nodeCoordinates(startNode), this.nodeCoordinates(targetNode)]

			arcMidCoordinates: (arc) ->
				startNode = this.nodes[arc.startNode]
				targetNode = this.nodes[arc.targetNode]
				return L.latLng((startNode.position[1] + targetNode.position[1]) / 2, (startNode.position[0] + targetNode.position[0]) / 2)

			pollutantColor: (arc, pollutant) ->
				rgb = hsv2rgb(0, arc.pollution[pollutant]/this.pollutionPeeks[pollutant], 1)
				return "rgb(#{Math.floor(rgb[0]*255)},#{Math.floor(rgb[1]*255)},#{Math.floor(rgb[2]*255)})"

			###
			setUpPollutionControls: (map) ->
				self = this
				this.fetchPollutionPeeks()
					.then(() ->
						baselayers = { }
						overlays = { }

						addPollutantBaseLayer = (pollutant) ->
							baselayers[pollutant] = self.$refs[pollutant][0].mapObject
							self.$refs[pollutant][0].mapObject.bringToFront()
						baselayers.None = self.$refs.None.mapObject
						self.$refs.None.mapObject.bringToFront()

						addPollutantBaseLayer(pollutant) for pollutant in self.pollutants
						L.control.layers(baselayers, overlays).addTo(map).expand();
					)
			###

			setUpPollutionControls: (map) ->
				baselayers = { }
				overlays = { }

				self = this
				addPollutantBaseLayer = (pollutant) ->
					baselayers[pollutant] = self.$refs[pollutant][0].mapObject
					self.$refs[pollutant][0].mapObject.bringToFront()

				baselayers.None = this.$refs.None.mapObject
				this.$refs.None.mapObject.bringToFront()

				addPollutantBaseLayer(pollutant) for pollutant in this.pollutants
				L.control.layers(baselayers, overlays).addTo(map).expand()

			selectArc: (arc, layerRef) ->
				this.selectedArc = arc
				console.log("Arc selected on layer #{layerRef} :")
				console.log(arc)
				unless layerRef == "None"
					this.$refs[layerRef][0].mapObject.openPopup(this.arcMidCoordinates(arc))
				else
					this.$refs.None.mapObject.openPopup(this.arcMidCoordinates(arc))

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

			updateArcPollution: (pollutedArc) ->
				arcToUpdate = this.arcs[pollutedArc.id]
				for pollutant in this.pollutants
					arcToUpdate.pollution[pollutant] = pollutedArc.pollution[pollutant]

			setUpWebSocket: (stompClient) ->
				self = this
				stompClient.subscribe('/simulation/arcs', (message) ->
						updatedArcs = JSON.parse(message.body)
						self.updateArc(arc) for arc in updatedArcs

						console.log(self.arcs)
					)

				stompClient.subscribe('/simulation/pollution_peeks', (message) ->
						updatedPollutionPeeks = JSON.parse(message.body)
						self.$set(self.pollutionPeeks, pollutant, value) for pollutant, value of updatedPollutionPeeks

						console.log(updatedPollutionPeeks)
						console.log(self.pollutionPeeks.NOx)
					)

				stompClient.subscribe('/simulation/pollution', (message) ->
						pollutedArc = JSON.parse(message.body)
						self.updateArcPollution(pollutedArc)
					)

			###
			fetchArcs: (nodes) ->
				this.nodes = nodes
				url = "/json-tests/arcs_43200.json"

				self = this
				fetch(url)
				.catch((error) ->
					console.log(error))
				.then((response) ->
					response.json()
					)
				.then((json) ->
					# Special vue syntax to make the dict responsive
					self.$set(self.arcs, id, arc) for id, arc of json

					console.log("Arcs :")
					console.log(self.arcs)
					)
			###

			###
			fetchPollutionPeeks: () ->
				url = "/json-tests/pollution_peeks_43200.json"

				self=this
				fetch(url)
				.catch((error) ->
					console.log(error)
					)
				.then((response) ->
					response.json()
				)
				.then((json) ->
					self.pollutionPeeks[pollutant] = value for pollutant, value of json
					self.pollutants.push(pollutant) for pollutant in Object.keys(json)
					console.log("Pollutants :")
					console.log(self.pollutants)

					console.log(self.pollutionPeeks)
				)
			###

</script>
