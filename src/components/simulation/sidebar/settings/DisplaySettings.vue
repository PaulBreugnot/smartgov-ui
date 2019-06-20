<template>
	<div class="w3-row-padding w3-margin-top w3-margin-bottom">
		<div class="w3-half">
			<input
				class="w3-check"
				type="checkbox"
				v-bind:checked="graph"
				v-on:change="$emit('update:graph',$event.target.checked)"
				/>
			<label>Graph</label>
		</div>
		<div class="w3-half">
			<input
				class="w3-check"
				type="checkbox"
				v-bind:checked="tiles"
				v-on:change="$emit('update:tiles',$event.target.checked)"
				/>
			<label>Tiles</label>
		</div>
	</div>
</template>

<script lang="coffee">

	export default
		props:
			graph:
				type: Boolean
				required: true
			tiles:
				type: Boolean
				required: true

</script>
