<template>
  <div class="w3-cell-row">
    <input class="w3-input w3-cell w3-cell-middle" type="number" min="1"></input>
    <div class="w3-cell w3-cell-middle">
      <a
        class="w3-button w3-theme-l2"
        v-on:click="showUnits = true">
        <i class="fa fa-caret-down" aria-hidden="true"></i>
        {{unit}}
      </a>
      <div
        class="w3-dropdown-content w3-bar-block w3-border"
        v-bind:class="[showUnits ? 'w3-show' : 'w3-hide']">
        <a class="w3-bar-item w3-button" v-on:click="selectUnit('Hours')">Hours</a>
        <a class="w3-bar-item w3-button" v-on:click="selectUnit('Days')">Days</a>
        <a class="w3-bar-item w3-button" v-on:click="selectUnit('Months')">Months</a>
      </div>
    </div>
  </div>
</template>

<script lang="coffee">

  export default

    data: () ->
      unit: "Days"
      showUnits: false

    methods:
      selectUnit: (unit) ->
        this.unit = unit
        this.showUnits = false
</script>
