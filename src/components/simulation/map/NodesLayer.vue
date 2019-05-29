<template>
  <l-featuregroup ref="nodeLayer">
    <l-circle
      v-for="(node, id) in nodes"
      v-on:click="openNodePopup(node)"
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
          self.$emit('ready', self.nodes)
        )

      openNodePopup: (node) ->
        this.selectedNode = node
        this.$refs.nodePopup.mapObject.openPopup(this.nodeCoordinates(this.selectedNode))

    mounted: () ->
      this.fetchNodes()

</script>
