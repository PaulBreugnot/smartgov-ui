<template>
  <div>
    <sidebar></sidebar>
    <div id="simulation-body">
      <toolbar></toolbar>
    </div>
  </div>
</template>

<script lang="coffee">
  import Toolbar from "./Toolbar"
  import Sidebar from "./Sidebar"

  export default
    components:
      "toolbar": Toolbar
      "sidebar": Sidebar
</script>

<style>
#simulation-body {
  margin-left: 15%;
}
</style>
