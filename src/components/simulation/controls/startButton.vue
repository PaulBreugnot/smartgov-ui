<template>
	<button v-on:click="startSimulation">
		<i v-if="running" class="fa fa-spinner fa-spin fa-fw fa-lg"></i>
		<i v-else class="fas fa-play fa-fw fa-lg"></i>
	</button>
</template>

<script lang=coffee>

export default

	props:
		simulationDuration:
			type: Number
			required: true
		tickDuration:
			type: Number
			required: true

	data: () ->
		"running": false

	methods:
		startSimulation: () ->
			parameters = "simulationDuration=#{this.simulationDuration}&tickDuration=#{this.tickDuration}"
			url = "#{process.env.VUE_APP_SIMULATION_SERVER_URL}/api/start?#{parameters}"

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
				self.$emit("sg-start")
				self.running = true
			)

		connectToWebSocket: () ->
			socket = new SockJS("#{process.env.VUE_APP_SIMULATION_SERVER_URL}/sg-websocket");
			stompClient = Stomp.over(socket);

			# Disable debug logs
			stompClient.debug = null

			self = this
			stompClient.connect({}, (frame) ->
				console.log('Connected: ' + frame);
				stompClient.subscribe('/simulation/stop', (message) ->
					console.log(JSON.parse(message.body))
					self.$emit("sg-stop")
					self.running = false
					)
			)

	mounted: () ->
		this.connectToWebSocket()

</script>
