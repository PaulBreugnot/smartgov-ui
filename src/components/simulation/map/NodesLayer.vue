<template>
	<l-featuregroup ref="nodesFeatureGroup">
			<!--
		<l-circle
			v-if="displaySettings.graph"
			v-for="(node, id) in nodes"
			v-on:click="selectNode(node)"
			:lat-lng="nodeCoordinates(node)"
			:radius="radius">
		</l-circle>
			<l-popup v-if="selectedNode">
				<p>id : {{selectedNode.id}}</p>
			</l-popup>
			-->
		<l-circle
			:lat-lng="[45.7753874, 4.778147]"
			:radius="20"
			/>
		<l-circle
			:lat-lng="[45.8712987, 4.8211324]"
			:radius="20"
			/>
		</l-featuregroup>
</template>

<script lang="coffee">
	import { LFeatureGroup, LCircle, LPopup } from "vue2-leaflet"

	export default
		components:
			"l-featuregroup": LFeatureGroup
			"l-circle": LCircle
			"l-popup": LPopup

		data: () ->
			selectedNode: null
			radius: 2
			nodes: {}

		props:
			displaySettings:
				type: Object
				required: true

		methods:
			nodeCoordinates: (node) ->
				return L.latLng(node.position[0], node.position[1])

			updateNode: (node) ->
				nodeToUpdate = this.nodes[node.id]
				if nodeToUpdate
					# The node is already initialized
					for key, value of node
						nodeToUpdate[key] = value

				else
					# this is a new node
					this.$set(this.nodes, node.id, node)

			setUpWebSocket: (stompClient) ->
				self = this
				stompClient.subscribe('/simulation/nodes', (message) ->
						updatedNodes = JSON.parse(message.body)
						self.updateNode(node) for node in updatedNodes

						console.log(self.nodes)
						self.$emit("nodes-updated", self.nodes)
					)

			fetchNodes: () ->
				url = process.env.VUE_APP_NODES

				self = this
				fetch(url)
				.catch((error) ->
					console.log(error)
					)
				.then((response) ->
					response.json()
					)
				.then((json) ->
					# Special vue syntax to make the dict responsive
					self.updateNode(node) for node in json

					console.log("Nodes :")
					console.log(json)
					console.log(self.nodes)
					self.$emit("nodes-updated", self.nodes)
				)

			selectNode: (node) ->
				this.selectedNode = node
				console.log("Node selected :")
				console.log(node)
				this.$refs.nodesFeatureGroup.mapObject.openPopup(this.nodeCoordinates(this.selectedNode))

			bringToFront: () ->
				this.$refs.nodesFeatureGroup.mapObject.bringToFront()

		mounted: () ->
			if process.env.VUE_APP_VISUALISATION_MODE == "static"
				this.fetchNodes()

</script>
