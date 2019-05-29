<template>
  <l-featuregroup>
    <!-- <l-polyline
      v-for="(arc, id) in arcs"
      :lat-lngs="arcCoordinates(arc)"
      color="green">
    </l-polyline> -->
    <l-featuregroup
      v-for="pollutant in pollutants"
      :ref="pollutant">
      <l-polyline
        v-for="(arc, id) in arcs"
        :lat-lngs="arcCoordinates(arc)"
        :color="pollutantColor(arc, pollutant)">
      </l-polyline>
    </l-featuregroup>
  </l-featuregroup>
</template>

<script lang="coffee">
  import { LFeatureGroup, LPolyline } from "vue2-leaflet"
  hsv2rgb = (h,s,v) ->
    f = (n) ->
      k = (n+h/60)%6
      return v-v*s*Math.max(Math.min(k,4-k,1),0);
    return [f(5),f(3),f(1)];

  export default

    components:
      "l-featuregroup": LFeatureGroup
      "l-polyline": LPolyline

    props:
      ["simulation-map"]

    data: () ->
      nodes: { }
      arcs: { }
      pollutants: null

    methods:
      nodeCoordinates: (node) ->
        return L.latLng(node.position[1], node.position[0])

      arcCoordinates: (arc) ->
        startNode = this.nodes[arc.startNode]
        targetNode = this.nodes[arc.targetNode]
        return [this.nodeCoordinates(startNode), this.nodeCoordinates(targetNode)]

      pollutantColor: (arc, pollutant) ->
        rgb = hsv2rgb(10, 0, 1)
        return "rgb(#{Math.floor(rgb[0]*255)},#{Math.floor(rgb[1]*255)},#{Math.floor(rgb[2]*255)})"

      setUpPollutionControls: (map) ->
        baselayers = { }
        overlays = { }

        self = this
        addPollutantBaseLayer = (pollutant) ->
          baselayers[pollutant] = self.$refs[pollutant][0].mapObject
          self.$refs[pollutant][0].mapObject.bringToFront()

        console.log overlays

        addPollutantBaseLayer(pollutant) for pollutant in this.pollutants
        L.control.layers(baselayers, overlays).addTo(map).expand();

      fetchArcs: (nodes) ->
        this.nodes = nodes
        url = "/json-tests/arcs_100.json"

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

          console.log("Arcs :")
          console.log(self.arcs)
          self.pollutants = Object.keys(Object.values(self.arcs)[0].pollution)
          console.log("Pollutants :")
          console.log(self.pollutants)
          )

</script>
