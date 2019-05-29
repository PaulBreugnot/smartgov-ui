<template>
  <l-map :zoom="zoom" :center="center">
    <l-tile-layer :url="url" :attribution="attribution"></l-tile-layer>
    <nodes-layer
      ref="nodesLayer"
      v-on:ready="handleNodesReady"
      />

    <arcs-layer
      ref="arcsLayer"
      />

    <agents-layer
      ref="agentsLayer"
      />

  </l-map>
</template>

<script lang="coffee">
import { LMap, LTileLayer } from "vue2-leaflet"
import NodesLayer from "./NodesLayer"
import ArcsLayer from "./ArcsLayer"
import AgentsLayer from "./AgentsLayer"

computeCenter = (nodes) ->
  nodeCount = Object.keys(nodes).length
  if  nodeCount > 0
    totalLat = 0
    totalLon = 0
    sumCoordinates = (coordinates) ->
      totalLat += coordinates[1]
      totalLon += coordinates[0]

    sumCoordinates(node.position) for id, node of nodes

    return  L.latLng(totalLat / nodeCount, totalLon / nodeCount)
  return L.latLng(0, 0)

export default
  components:
    "l-map": LMap
    "l-tile-layer": LTileLayer
    "nodes-layer": NodesLayer
    "arcs-layer": ArcsLayer
    "agents-layer": AgentsLayer

  data: () ->
      zoom:13
      url:'http://{s}.tile.osm.org/{z}/{x}/{y}.png'
      attribution:'&copy; <a href="http://osm.org/copyright">OpenStreetMap</a> contributors'
      center: L.latLng(0, 0)

  methods:
    handleNodesReady: (nodes) ->
      self = this

      this.center = computeCenter(nodes)
      this.$refs.agentsLayer.fetchAgents(nodes)
      this.$refs.arcsLayer.fetchArcs(nodes)
        .then(() ->
          self.$refs.agentsLayer.bringToFront()
        )

</script>

<style>

</style>
