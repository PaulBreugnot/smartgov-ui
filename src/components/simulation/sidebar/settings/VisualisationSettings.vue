<template>
	<form class="w3-container w3-padding-16 w3-animate-zoom">
		<label>Tick delay (ms) :</label>
		<period-input
			v-bind:min="10"
			v-bind:max="5000"
			v-bind:init="100"
			v-on:update:period="updateTickDelay"/>
		<label>Agents refresh rate :</label>
		<period-input
			v-bind:max="simulationDuration"
			v-on:update:period="updateAgentsRefreshPeriod"
			/>
		<label>Pollution refresh rate :</label>
		<period-input
			v-bind:max="simulationDuration"
			v-on:update:period="updatePollutionRefreshPeriod"
			/>
	</form>
</template>

<script lang="coffee">

	import PeriodInput from "../tools/PeriodInput"

	export default

		props:
			simulationDuration:
				type: Number
				required: true

		components:
			"period-input": PeriodInput

		methods:

			updateTickDelay: (tickDelay) ->
				url = "#{process.env.VUE_APP_SIMULATION_SERVER_URL}/api/tick_delay?delay=#{tickDelay}"

				options =
					method: "PUT"

				fetch(url, options)
				.catch((error) ->
					console.log(error)
				)

			updateAgentsRefreshPeriod: (agentsRefreshPeriod) ->
				url = "#{process.env.VUE_APP_SIMULATION_SERVER_URL}/api/agents_refresh_period?period=#{agentsRefreshPeriod}"

				options =
					method: "PUT"

				fetch(url, options)
				.catch((error) ->
					console.log(error)
				)

			updatePollutionRefreshPeriod: (pollutionRefreshPeriod) ->
				url = "#{process.env.VUE_APP_SIMULATION_SERVER_URL}/api/pollution_refresh_period?period=#{pollutionRefreshPeriod}"

				options =
					method: "PUT"

				fetch(url, options)
				.catch((error) ->
					console.log(error)
				)

</script>
