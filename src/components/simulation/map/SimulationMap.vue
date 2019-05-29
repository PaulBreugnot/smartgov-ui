<template>
  <l-map ref="simulationMap" :zoom="zoom" :center="center">
    <l-tile-layer ref="osmBaseLayer" :url="url" :attribution="attribution"></l-tile-layer>
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
import { LMap, LTileLayer, LControlLayers } from "vue2-leaflet"
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
    "l-control-layers": LControlLayers
    "nodes-layer": NodesLayer
    "arcs-layer": ArcsLayer
    "agents-layer": AgentsLayer

  data: () ->
      zoom:13
      url:'http://{s}.tile.osm.org/{z}/{x}/{y}.png'
      attribution:'&copy; <a href="http://osm.org/copyright">OpenStreetMap</a> contributors'
      center: L.latLng(0, 0)

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

      overlays = { }
      L.control.layers(baseLayers, overlays).addTo(this.$refs.simulationMap.mapObject).expand();

    handleNodesReady: (nodes) ->
      self = this

      this.center = computeCenter(nodes)
      this.$refs.agentsLayer.fetchAgents(nodes)
      this.$refs.arcsLayer.fetchArcs(nodes)
        .then(() ->
          self.$refs.nodesLayer.bringToFront()
          self.$refs.agentsLayer.bringToFront()
          self.setUpControls()

          self.$refs.arcsLayer.setUpPollutionControls(self.$refs.simulationMap.mapObject)
        )

</script>

<style>

</style>
