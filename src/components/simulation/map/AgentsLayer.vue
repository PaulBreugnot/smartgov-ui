<template>
  <l-feature-group ref="agentsFeatureGroup">
    <l-circle
      v-for="(agent, id) in agents"
      :lat-lng="agentCoordinates(agent)"
      :radius="radius"
      v-on:click="openPopup(agent)"
      color="red">
    </l-circle>
    <l-popup v-if="selectedAgent">
      <p>id : {{selectedAgent.id}}</p>
      <p>speed : {{selectedAgent.body.speed}}</p>
      <p>origin : {{selectedAgent.properties.beginNode}}</p>
      <p>destination : {{selectedAgent.properties.endNode}}</p>
    </l-popup>
    <l-polyline
      v-if="selectedAgent"
      :lat-lngs="trajectoryCoordinates(selectedAgent)"
      color="red"/>
  </l-feature-group>
</template>

<script lang="coffee">
  import { LFeatureGroup, LCircle, LPopup, LPolyline } from "vue2-leaflet"

  export default

    components:
      "l-feature-group": LFeatureGroup
      "l-circle": LCircle
      "l-popup": LPopup
      "l-polyline": LPolyline

    data: () ->
      radius: 3
      nodes: {}
      agents: {}
      selectedAgent: null

    methods:
      fetchAgents: (nodes) ->
        this.nodes = nodes
        url = "json-tests/agents_100.json"

        self = this
        fetch(url)
        .catch((error) ->
          console.log(error)
          )
        .then((response) ->
          response.json()
          )
        .then((json) ->
          console.log(json)
          self.$set(self.agents, id, agent) for id, agent of json
          self.$refs.agentsFeatureGroup.mapObject.bringToFront()
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

      openPopup: (agent) ->
        this.selectedAgent = agent
        this.$refs.agentsFeatureGroup.mapObject.openPopup(this.agentCoordinates(this.selectedAgent))

</script>
