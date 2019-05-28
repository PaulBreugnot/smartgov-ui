<template>
  <l-map :zoom="zoom" :center="center">
    <l-tile-layer :url="url" :attribution="attribution"></l-tile-layer>
    <l-featuregroup ref="nodeLayer">
      <l-circle
        v-for="(node, id) in nodes"
        v-on:click="openNodePopup(node)"
        :lat-lng="nodeCoordinates(node)"
        :radius="radius"></l-circle>
    </l-featuregroup>

    <l-polyline
      v-for="(arc, id) in arcs"
      :lat-lngs="arcCoordinates(arc)"
      color="green">
      </l-polyline>
    <agents-layer
      ref="agentsLayer"
      v-bind:nodes="nodes"/>
    <l-featuregroup ref="nodePopup">
      <l-popup v-if="selectedNode">
        <p>id : {{selectedNode.id}}</p>
      </l-popup>
    </l-featuregroup>
  </l-map>
</template>

<script lang="coffee">
import {LMap, LFeatureGroup, LTileLayer, LCircle, LPolyline, LPopup} from "vue2-leaflet"
import AgentsLayer from "./AgentsLayer"

export default
  components:
    "l-map": LMap
    "l-tile-layer": LTileLayer
    "l-circle": LCircle
    "l-polyline": LPolyline
    "l-featuregroup": LFeatureGroup
    "l-popup": LPopup
    "agents-layer": AgentsLayer

  computed:
    center: () ->
      nodeCount = Object.keys(this.nodes).length
      if  nodeCount > 0
        totalLat = 0
        totalLon = 0
        sumCoordinates = (coordinates) ->
          totalLat += coordinates[1]
          totalLon += coordinates[0]

        sumCoordinates(node.position) for id, node of this.nodes

        return  L.latLng(totalLat / nodeCount, totalLon / nodeCount)
      return L.latLng(0, 0)

  data: () ->
      zoom:13
      url:'http://{s}.tile.osm.org/{z}/{x}/{y}.png'
      attribution:'&copy; <a href="http://osm.org/copyright">OpenStreetMap</a> contributors'
      radius: 2
      nodes: {} # A dict(id, node)
      selectedNode: null
      arcs: {} # A dict(id, arc)

  methods:
    nodeCoordinates: (node) ->
      return L.latLng(node.position[1], node.position[0])

    arcCoordinates: (arc) ->
      startNode = this.nodes[arc.startNode]
      unless startNode.id == arc.startNode
        console.log("FATAL ERROR")
      targetNode = this.nodes[arc.targetNode]
      unless targetNode.id == arc.targetNode
        console.log("FATAL ERROR")
      return [this.nodeCoordinates(startNode), this.nodeCoordinates(targetNode)]

    openNodePopup: (node) ->
      this.selectedNode = node
      this.$refs.nodePopup.mapObject.openPopup(this.nodeCoordinates(this.selectedNode))

    fetchNodes: () ->
      url = "/json-tests/init_nodes.json"

      self = this
      fetch(url)
      .catch((error) ->
        console.log(error)
        )
      .then((response) ->
        console.log(response)
        response.json()
        )
      .then((json) ->
        # Special vue syntax to make the dict responsive
        self.$set(self.nodes, id, node) for id, node of json

        # Because arcs are defined by references to nodes, arcs are fetched once
        # nodes have been fetched.
        self.fetchArcs()
      )

    fetchArcs: () ->
      url = "/json-tests/init_arcs.json"

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
        self.$refs.agentsLayer.fetchAgents()

        self.$refs.nodeLayer.mapObject.bringToFront()
        )

  mounted: () ->
    this.fetchNodes()

</script>

<style>

</style>
