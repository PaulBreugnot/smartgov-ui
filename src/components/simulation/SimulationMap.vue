<template>
  <l-map :zoom="zoom" :center="center">
    <l-tile-layer :url="url" :attribution="attribution"></l-tile-layer>
    <l-circle v-for="marker in markers" :lat-lng="marker" :radius="radius"></l-circle>
    <l-polyline
      :lat-lngs="round"
      color="green">
      </l-polyline>
  </l-map>
</template>

<script lang="coffee">
import {LMap, LTileLayer, LCircle, LPolyline} from "vue2-leaflet"

export default
  components:
    "l-map": LMap
    "l-tile-layer": LTileLayer
    "l-circle": LCircle
    "l-polyline": LPolyline

  data: () ->
      zoom:13
      center: L.latLng(45.7578137, 4.8320114)
      url:'http://{s}.tile.osm.org/{z}/{x}/{y}.png'
      attribution:'&copy; <a href="http://osm.org/copyright">OpenStreetMap</a> contributors'
      radius: 100
      markers: [
        L.latLng(45.7578137, 4.8320114),
        L.latLng(45.765269, 4.872049),
        L.latLng(45.754703, 4.852873),
        L.latLng(45.754213, 4.859141),
        L.latLng(45.759863, 4.851673),
        L.latLng(45.751412, 4.8574),
        L.latLng(45.748631, 4.858768),
      ]

      round: [
        L.latLng(45.7578137, 4.8320114),
        L.latLng(45.765269, 4.872049),
        L.latLng(45.754213, 4.859141),
        L.latLng(45.754703, 4.852873),
        L.latLng(45.759863, 4.851673),
        L.latLng(45.751412, 4.8574),
        L.latLng(45.748631, 4.858768),
        L.latLng(45.7578137, 4.8320114)
      ]

</script>

<style>

</style>
