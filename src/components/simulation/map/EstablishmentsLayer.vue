<template>
	<l-featuregroup ref="establishmentsFeatureGroup">
		<l-circle
			v-if="displaySettings.establishments"
			v-for="(establishment, id) in establishments"
			v-on:click="selectEstablishment(establishment)"
			:lat-lng="establishmentCoordinates(establishment)"
			:radius="12"
			color="orange">
		</l-circle>
		<l-polyline
			v-if="selectedEstablishment"
			:lat-lngs="roundCoordinates(selectedEstablishment)"
			color="blue"/>

		<l-popup v-if="selectedEstablishment">
			<p>id : {{selectedEstablishment.id}}</p>
			<p>name : {{selectedEstablishment.name}}</p>
			<p>st8 : {{selectedEstablishment.activity}}</p>
			<p>fleet :</p>
			<p v-for="(count, norm) in fleet">{{norm}} : {{count}}</p>
		</l-popup>
	</l-featuregroup>
</template>

<script lang="coffee">
	import { LFeatureGroup, LCircle, LPopup, LPolyline } from "vue2-leaflet"

	export default
		props:
			displaySettings:
				type: Object
				required: true

		components:
			"l-featuregroup": LFeatureGroup
			"l-circle": LCircle
			"l-popup": LPopup
			"l-polyline": LPolyline

		data: () ->
			selectedEstablishment: null
			establishments: {}

		computed:
			fleet: () ->
				fleet =
					"CONVENTIONAL" : 0
					"EURO1": 0
					"EURO2": 0
					"EURO3": 0
					"EURO4": 0
					"EURO5": 0
					"EURO6": 0
				for vehicle in Object.values(this.selectedEstablishment.fleet)
					do(vehicle) ->
						fleet[vehicle.euroNorm] += 1
				return fleet

		methods:
			establishmentCoordinates: (establishment) ->
				return L.latLng(establishment.location[0], establishment.location[1])

			selectEstablishment: (establishment) ->
				this.selectedEstablishment = establishment
				console.log("Establishment selected :")
				console.log(establishment)
				this.$refs.establishmentsFeatureGroup.mapObject.openPopup(this.establishmentCoordinates(this.selectedEstablishment))

			roundCoordinates: (establishment) ->
				coordinates = [this.establishmentCoordinates(establishment)]
				self = this
				console.log Object.values(establishment.rounds)
				for round in Object.values(establishment.rounds)
					do (round) ->
						console.log round
						for id in round.establishments
							do (id) ->
								coordinates.push(self.establishmentCoordinates(self.establishments[id]))

				coordinates.push(this.establishmentCoordinates(establishment))
				return coordinates

			updateEstablishment: (establishment) ->
				establishmentToUpdate = this.establishments[establishment.id]
				if establishmentToUpdate
					# The establishment is already initialized
					for key, value of establishment
						establishmentToUpdate[key] = value

				else
					# this is a new establishment
					this.$set(this.establishments, establishment.id, establishment)

			fetchEstablishments: () ->
				url = process.env.VUE_APP_ESTABLISHMENTS

				self = this
				fetch(url)
				.catch((error) ->
					console.log(error)
					)
				.then((response) ->
					response.json()
					)
				.then((json) ->
					# Special vue syntax to make the dict responsive
					self.updateEstablishment(establishment) for establishment in json

					console.log("Establishments :")
					console.log(json)
					console.log(self.establishments)
				)

		mounted: () ->
			if process.env.VUE_APP_VISUALISATION_MODE == "static"
				this.fetchEstablishments()
</script>


