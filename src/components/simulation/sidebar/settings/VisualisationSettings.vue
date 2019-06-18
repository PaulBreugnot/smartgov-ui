<template>
	<form class="w3-container w3-padding-16 w3-animate-zoom">
		<div class="w3-container w3-center w3-margin-bottom">
			<input class="w3-check" type="checkbox" v-model="disabled" v-on:change="disableVisualisation($event.target.checked)">
			<label>Disable</label>
		</div>

		<label>Tick delay (ms) :</label>
		<period-input
			ref="tickDelayInput"
			v-bind:disabled="disabled"
			v-bind:min="10"
			v-bind:max="5000"
			v-bind:init="100"
			v-on:update:period="updateTickDelay"/>

		<label>Agents refresh rate :</label>
		<period-input
			ref="agentsRefreshPeriodInput"
			v-bind:disabled="disabled"
			v-bind:max="simulationDuration"
			v-on:update:period="updateAgentsRefreshPeriod"
			/>

		<label>Pollution refresh rate :</label>
		<period-input
			ref="pollutionRefreshPeriodInput"
			v-bind:disabled="disabled"
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

		data: () ->
			disabled: false

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
			
			disableVisualisation: (disabled) ->
				if disabled
					console.log "Disabling visualisation"
					url = "#{process.env.VUE_APP_SIMULATION_SERVER_URL}/api/enable_visualisation?enabled=false"

					options =
						method: "PUT"

					fetch(url, options)
					.catch((error) ->
						console.log error
					)

				else
					console.log "Enabling visualisation"

					url = "#{process.env.VUE_APP_SIMULATION_SERVER_URL}/api/enable_visualisation?enabled=true"

					options =
						method: "PUT"

					fetch(url, options)
					.catch((error) ->
						console.log error
					)

					this.updateTickDelay(this.$refs.tickDelayInput.period)


</script>
