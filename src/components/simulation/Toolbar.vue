<template>
  <div class="w3-container w3-light-grey">
    <div class="w3-cell">
      <button class="run-button w3-button w3-round-large w3-green w3-tiny"><i class="fas fa-play"></i></button>
    </div>
    <div class="w3-cell">
      <button class="run-button w3-button w3-round-large w3-yellow w3-tiny"><i class="fas fa-pause"></i></button>
    </div>
    <div class="w3-cell">
      <button class="run-button w3-button w3-round-large w3-red w3-tiny"><i class="fas fa-stop"></i></button>
    </div>
    <div class="w3-dropdown-hover w3-cell w3-hover-theme">
      <button class="w3-button">Scenario</button>
      <div class="w3-dropdown-content w3-bar-block w3-card-4">
        <button class="w3-bar-item w3-button">Visualization</button>
        <button class="w3-bar-item w3-button">Deliveries</button>
        <button class="w3-bar-item w3-button">Pollution</button>
      </div>
    </div>
  </div>
</template>

<script lang="coffee">

</script>

<style>

.run-button {
  margin: 0px 4px;
}

</style>
