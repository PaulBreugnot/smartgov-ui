<template>
	<l-feature-group>
		<l-feature-group
			v-for="tileLine in computedTiles">
			<pollution-tile
				v-for="tile in tileLine"
				v-bind:arcs="tile.arcs"
				v-bind:bounds="tile.bounds"
				v-bind:pollution-peek="pollutionPeek"
				v-on:click="$emit('select-arcs', tile.arcs)"
				v-on:update:pollution-peek="updatePollutionPeek($event)"
				/>
		</l-feature-group>
	</l-feature-group>
</template>

<script lang="coffee">
	import {LFeatureGroup} from "vue2-leaflet"
	import PollutionTile from "./PollutionTile"

	export default
		components:
			"l-feature-group": LFeatureGroup
			"pollution-tile": PollutionTile
		
		props:
			pollutant:
				type: String
				required: true

			nodes:
				type: Object
				required: true

			arcs:
				type: Object
				required: true

			lMap:
				type: Object
				required: true

		data: () ->
			tiles: []
			pollutionPeek: 0

		computed:
			latLngBoundingBox: () ->
				if Object.values(this.nodes).length
					console.log "Computing bounding box..."
					top = -Infinity
					bottom = Infinity
					left = Infinity
					right = -Infinity
					for id, node of this.nodes
						do (id, node) ->
							if node.position[1] < bottom
								bottom = node.position[1]
							if node.position[1] > top
								top = node.position[1]
							if node.position[0] < left
								left = node.position[0]
							if node.position[0] > right
								right = node.position[0]

					corner1 = [top, left]
					corner2 = [bottom, right]
					return [corner1, corner2]
				return null

			computedTiles: () ->
				boundingBox = this.latLngBoundingBox
				tiles = []

				if boundingBox
					tileWidthCount = 8
					boundingBoxWidth = boundingBox[1][1] - boundingBox[0][1]
					tileSize = boundingBoxWidth / tileWidthCount

					boundingBoxHeight = boundingBox[0][0] - boundingBox[1][0]
					tileHeightCount = Math.floor(boundingBoxHeight / tileSize) + 1

					origin = L.latLng(boundingBox[0][0] + tileSize, boundingBox[0][1] - tileSize)

					self = this
					for i in [0...tileHeightCount + 2]
						do (i) ->
							tileLine = []
							for j in [0...tileWidthCount + 2]
								do (j) ->
									corner1 = L.latLng(origin.lat - i * tileSize, origin.lng + j * tileSize)
									corner2 = L.latLng(origin.lat - (i + 1) * tileSize, origin.lng + (j + 1) * tileSize)
									tileLine.push(
										bounds: [corner1, corner2]
										arcs: []
									)
							tiles.push(tileLine)

				this.computeArcsInTiles(tiles)

				for tileLine in tiles
					do (tileLine) ->
						for i in [0...tileLine.length]
							do (i) ->
								while i < tileLine.length and tileLine[i].arcs.length == 0
									tileLine.splice(i, 1)

				return tiles

		methods:
			computeArcsInTiles: (tiles) ->

				lookForLine = (min, max, lat) ->
					mid = Math.floor((min + max) / 2)
					currentLine = tiles[mid]
					currentTile = tiles[mid][0]

					# /!\ Latitudes are in decreasing order
					if lat <= currentTile.bounds[0].lat and lat >= currentTile.bounds[1].lat
						# lat is contained in the line
						return currentLine

					# /!\ Latitudes are in decreasing order
					if lat < currentTile.bounds[0].lat
						return lookForLine(mid + 1, max, lat)
					return lookForLine(min, mid, lat)

				lookForTileInLine = (min, max, lng, line) ->
					mid = Math.floor((min + max) / 2)
					currentTile = line[mid]
					if lng >= currentTile.bounds[0].lng and lng <= currentTile.bounds[1].lng
						# lng is contained in the tile
						return currentTile
					
					if lng > currentTile.bounds[1].lng
						return lookForTileInLine(mid + 1, max, lng, line)
					return lookForTileInLine(min, mid, lng, line)

				console.log "Assigning arcs to tiles..."
				self = this
				for id, arc of this.arcs
					do (id, arc) ->
						sourceNode = self.nodes[arc.startNode].position
						
						tileLine = lookForLine(0, tiles.length - 1, sourceNode[1])
						tile = lookForTileInLine(0, tileLine.length - 1, sourceNode[0], tileLine)

						tile.arcs.push(arc)
				console.log "Done."

			updatePollutionPeek: (peek) ->
				if peek > this.pollutionPeek
					this.pollutionPeek = peek

</script>
