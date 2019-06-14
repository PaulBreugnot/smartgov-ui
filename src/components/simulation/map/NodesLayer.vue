<template>
	<l-featuregroup ref="nodesFeatureGroup">
		<l-circle
			v-for="(node, id) in nodes"
			v-on:click="selectNode(node)"
			:lat-lng="nodeCoordinates(node)"
			:radius="radius">
		</l-circle>
			<l-popup v-if="selectedNode">
				<p>id : {{selectedNode.id}}</p>
			</l-popup>
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

		methods:
			nodeCoordinates: (node) ->
				return L.latLng(node.position[1], node.position[0])

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

			###
			fetchNodes: () ->
				url = "/json-tests/init_nodes.json"

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
					self.$set(self.nodes, id, node) for id, node of json

					console.log("Nodes :")
					console.log(self.nodes)
					self.$emit('ready', self.nodes)
				)
			###

			selectNode: (node) ->
				this.selectedNode = node
				console.log("Node selected :")
				console.log(node)
				this.$refs.nodesFeatureGroup.mapObject.openPopup(this.nodeCoordinates(this.selectedNode))

			bringToFront: () ->
				this.$refs.nodesFeatureGroup.mapObject.bringToFront()

		###
		mounted: () ->
			this.fetchNodes()
		###

</script>
