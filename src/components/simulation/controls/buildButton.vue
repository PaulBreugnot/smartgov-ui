<template>
    <button v-on:click="buildSimulation"><i class="fas fa-cogs fa-lg"></i></button>
</template>

<script lang=coffee>

export default
	
	methods:
		buildSimulation: () ->
			url = "http://localhost:8000/api/build"
			options =
				method: "PUT"

			fetch(url, options)
			.catch((error) ->
				console.log(error)
			)
			.then((response) ->
				response.text()
			)
			.then((message) ->
				console.log(message)
			)

</script>
