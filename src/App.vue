<template>
  <div id="app">
    <h1 class="title has-text-centered">Bedroom Profit</h1>
    <BedRoom />
  </div>
</template>

<script>
import BedRoom from './components/BedRoom.vue'
//Import BedRoom only
export default {
  name: 'App',
  components: {
    BedRoom
  }
}

document.title = "Bedroom Profit";
</script>



<style lang="scss">
//import Bulma
  @import 'bulma';
  @import '~bulma-tooltip';

  #app {
    margin-top: 25px;
  }

  .fa + *{
    margin-left: 5px;
  }

  .select {
    &,
    select {
      width: 100%;
    }
  }
</style>
