<template>
	<button v-on:click="toggleSimulationState">
		<i v-if="paused" class="fa fa-play fa-fw fa-lg"></i>
		<i v-else class="fa fa-pause fa-fw fa-lg"></i>
	</button>
</template>

<script lang=coffee>

export default

	props:
		["paused"]

	methods:
		toggleSimulationState: () ->
			if(this.paused)
				this.resumeSimulation()
			else
				this.pauseSimulation()

		pauseSimulation: () ->
			url = "#{process.env.VUE_APP_SIMULATION_SERVER_URL}/api/pause"
			options =
				method: "PUT"

			self = this
			fetch(url, options)
			.catch((error) ->
				console.log(error)
			)
			.then((response) ->
				response.text()
			)
			.then((message) ->
				console.log(message)
				self.$emit("sg-paused")
			)

		resumeSimulation: () ->
			url = "#{process.env.VUE_APP_SIMULATION_SERVER_URL}/api/resume"
			options =
				method: "PUT"

			self = this
			fetch(url, options)
			.catch((error) ->
				console.log(error)
			)
			.then((response) ->
				response.text()
			)
			.then((message) ->
				console.log(message)
				self.$emit("sg-resumed")
			)

</script>
