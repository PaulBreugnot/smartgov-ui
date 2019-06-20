<template>
	<l-map ref="simulationMap" :zoom="zoom" :center="center">
		<l-tile-layer ref="osmBaseLayer" :url="url" :attribution="attribution"></l-tile-layer>
	
	<l-feature-group ref="graphLayer">
			<nodes-layer
				ref="nodesLayer"
				v-on:nodes-updated="handleNodesReady"
				/>

			<arcs-layer
				v-if="lMap"
				ref="arcsLayer"
				v-bind:display-settings="displaySettings"
				v-bind:l-map="lMap"
				/>
	</l-feature-group>

	<agents-layer
		ref="agentsLayer"
		/>

	</l-map>
</template>

<script lang="coffee">
import { LMap, LTileLayer, LControlLayers, LFeatureGroup } from "vue2-leaflet"
import NodesLayer from "./NodesLayer"
import ArcsLayer from "./ArcsLayer"
import AgentsLayer from "./AgentsLayer"

computeCenter = (nodes) ->
	nodeCount = Object.keys(nodes).length
	if	nodeCount > 0
		totalLat = 0
		totalLon = 0
		sumCoordinates = (coordinates) ->
			totalLat += coordinates[1]
			totalLon += coordinates[0]

		sumCoordinates(node.position) for id, node of nodes

		return	L.latLng(totalLat / nodeCount, totalLon / nodeCount)
	return L.latLng(0, 0)

export default
	components:
		"l-map": LMap
		"l-tile-layer": LTileLayer
		"l-control-layers": LControlLayers
		"l-feature-group": LFeatureGroup
		"nodes-layer": NodesLayer
		"arcs-layer": ArcsLayer
		"agents-layer": AgentsLayer

	props:
		displaySettings:
			type: Object
			required: true

	data: () ->
		lMap: null
		zoom:13
		url:'http://{s}.tile.osm.org/{z}/{x}/{y}.png'
		attribution:'&copy; <a href="http://osm.org/copyright">OpenStreetMap</a> contributors'
		center: L.latLng(0, 0)
	
	watch:
		"displaySettings.graph": (oldVal, newVal) ->
			console.log this.displaySettings

	methods:
		setUpControls: () ->
			L.GridLayer.BlankLayer = L.GridLayer.extend(
				createTile: (coords) ->
					tile = L.DomUtil.create('canvas');
					return tile;
			)

			L.gridLayer.blank = () ->
				new L.GridLayer.BlankLayer()

			newLayer = L.gridLayer.blank().addTo(this.$refs.simulationMap.mapObject)
			baseLayers =
				"Blank": newLayer
				"Osm": this.$refs.osmBaseLayer.mapObject

			overlays = {}
				# "Graph": this.$refs.graphLayer.mapObject

			L.control.layers(baseLayers, overlays).addTo(this.$refs.simulationMap.mapObject).expand();

		handleNodesReady: (nodes) ->
			self = this

			console.log("Updating nodes...")

			this.center = computeCenter(nodes)

			this.$refs.arcsLayer.setUpNodes(nodes)
			this.$refs.agentsLayer.setUpNodes(nodes)

		connectToWebSocket: () ->
			socket = new SockJS("#{process.env.VUE_APP_SIMULATION_SERVER_URL}/sg-websocket");
			stompClient = Stomp.over(socket);

			# Disable debug logs
			stompClient.debug = null

			self = this
			stompClient.connect({}, (frame) ->
				console.log('Connected: ' + frame)
				self.$refs.nodesLayer.setUpWebSocket(stompClient)
				self.$refs.arcsLayer.setUpWebSocket(stompClient)
				self.$refs.agentsLayer.setUpWebSocket(stompClient)
			)

	mounted: () ->
		this.connectToWebSocket()

		self = this
		this.$nextTick(() ->
			self.lMap = self.$refs.simulationMap.mapObject
			self.setUpControls()
#			self.$refs.arcsLayer.setUpPollutionControls(self.$refs.simulationMap.mapObject)
			)

</script>

<style>

</style>
