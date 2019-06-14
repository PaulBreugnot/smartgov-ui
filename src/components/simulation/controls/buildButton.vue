<template>
	<button v-on:click="buildSimulation"><i class="fas fa-cogs fa-lg"></i></button>
</template>

<script lang=coffee>

export default
	
	methods:
		buildSimulation: () ->
			url = "#{process.env.VUE_APP_SIMULATION_SERVER_URL}/api/build"
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
				self.$emit("sg-built")
			)

</script>
