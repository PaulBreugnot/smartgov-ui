<template>
    <button v-on:click="startSimulation"><i class="fas fa-play"></i></button>
</template>

<script lang=coffee>

export default

	props:
		["duration"]
	
	methods:
		startSimulation: () ->
			url = "http://localhost:8000/api/start?ticks=#{this.duration}"
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
