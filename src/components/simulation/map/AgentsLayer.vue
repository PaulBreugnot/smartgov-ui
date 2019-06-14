<template>
	<l-featuregroup ref="agentsFeatureGroup">
		<l-circle
			v-for="(agent, id) in agents"
			:lat-lng="agentCoordinates(agent)"
			:radius="radius"
			v-on:click="selectAgent(agent)"
			:color="id == selectedAgentId ? 'red' : 'purple'">
		</l-circle>
		<l-popup v-if="selectedAgent">
			<p>id : {{selectedAgent.id}}</p>
			<p>speed : {{selectedAgent.body.speed}}</p>
			<p>origin : {{selectedAgent.behavior.origin}}</p>
			<p>destination : {{selectedAgent.behavior.destination}}</p>
		</l-popup>
		<l-polyline
			v-if="selectedAgent"
			:lat-lngs="trajectoryCoordinates(selectedAgent)"
			color="blue"/>
	</l-featuregroup>
</template>

<script lang="coffee">
import { LFeatureGroup, LCircle, LPopup, LPolyline } from "vue2-leaflet"

export default

	components:
		"l-featuregroup": LFeatureGroup
		"l-circle": LCircle
		"l-popup": LPopup
		"l-polyline": LPolyline

	data: () ->
		radius: 3
		nodes: {}
		agents: {}
		selectedAgentId: null
		selectedAgent: null

	methods:
		###
		fetchAgents: (nodes) ->
			this.nodes = nodes
			url = "json-tests/agents_43200.json"

			self = this
			fetch(url)
			.catch((error) ->
				console.log(error)
			)
			.then((response) ->
				response.json()
			)
			.then((json) ->
				self.$set(self.agents, id, agent) for id, agent of json
				console.log("Agents :")
				console.log(self.agents)
			)
		###

		setUpNodes: (nodes) ->
			this.nodes = nodes

		updateAgent: (agent) ->
			agentToUpdate = this.agents[agent.id]
			if agentToUpdate
				# The agent is already initialized
				for key, value of agent
					agentToUpdate[key] = value

			else
				# this is a new agent
				this.$set(this.agents, agent.id, agent)

		setUpWebSocket: (stompClient) ->
			self = this
			stompClient.subscribe('/simulation/agents', (message) ->
					updatedAgents = JSON.parse(message.body)
					self.updateAgent(agent) for agent in updatedAgents
				)

		agentCoordinates: (agent) ->
			position = agent.body.position
			return L.latLng(position[1], position[0])

		trajectoryCoordinates: (agent) ->
			points = []
			self = this
			addPoint = (nodeId) ->
				node = self.nodes[nodeId]
				points.push(L.latLng(node.position[1], node.position[0]))

			addPoint(nodeId) for nodeId in agent.body.plan.nodes
			return points

		bringToFront: () ->
			this.$refs.agentsFeatureGroup.mapObject.bringToFront()

		selectAgent: (agent) ->
			this.selectedAgentId = agent.id
			this.selectedAgent = agent
			console.log("Selected agent :")
			console.log(agent)
			this.$refs.agentsFeatureGroup.mapObject.openPopup(this.agentCoordinates(this.selectedAgent))

</script>
