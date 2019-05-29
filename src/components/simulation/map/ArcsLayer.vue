<template>
  <l-featuregroup>
    <l-polyline
      v-for="(arc, id) in arcs"
      :lat-lngs="arcCoordinates(arc)"
      color="green">
    </l-polyline>
  </l-featuregroup>
</template>

<script lang="coffee">
  import { LFeatureGroup, LPolyline } from "vue2-leaflet"

  export default

    components:
      "l-featuregroup": LFeatureGroup
      "l-polyline": LPolyline

    data: () ->
      nodes: { }
      arcs: { }

    methods:
      nodeCoordinates: (node) ->
        return L.latLng(node.position[1], node.position[0])

      arcCoordinates: (arc) ->
        startNode = this.nodes[arc.startNode]
        targetNode = this.nodes[arc.targetNode]
        return [this.nodeCoordinates(startNode), this.nodeCoordinates(targetNode)]

      fetchArcs: (nodes) ->
        this.nodes = nodes
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

          # self.$refs.nodeLayer.mapObject.bringToFront()

          console.log("arcs : #{self.arcs}")
          )
</script>
