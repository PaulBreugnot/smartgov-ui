<template>
	<button v-on:click="stop">
		<i class="fa fa-stop fa-fw fa-lg"></i>
	</button>
</template>

<script lang=coffee>

export default

	methods:
		stop: () ->
			url = "#{process.env.VUE_APP_SIMULATION_SERVER_URL}/api/stop"
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
				self.$emit("sg-stop")
			)

</script>
