<template>
	<button v-on:click="step">
		<i class="fa fa-forward fa-fw fa-lg"></i>
	</button>
</template>

<script lang=coffee>

export default

	methods:
		step: () ->
			url = "#{process.env.VUE_APP_SIMULATION_SERVER_URL}/api/step"
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
				self.$emit("sg-step")
			)

</script>
