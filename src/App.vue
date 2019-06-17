<template>
	<div id="app">
		<header id="main-header" class="w3-bar w3-theme-d1">
			<h1 class="w3-bar-item">SmartGov UI</h1>
			<h3 class="w3-bar-item w3-right">OpenSource Multi-agent Simulator for Urban Policies Optimization</h3>
		</header>
		<model/>
	</div>
</template>

<script lang="coffee">
import Model	from "./components/simulation/Model"

export default

	name: 'app'

	components:
		"Model": Model

</script>

<style>
h3 {
	font-style: italic;
}
html, body, #app {
	height: 100%;
}

#main-header {
	height: 8%;
}
</style>
