<template>
  <router-view/>
</template>

<script>
import Localbase from 'localbase'

export default {
  name: 'App',
  setup () {
    let db = new Localbase('db')
  }
}

</script>

<style lang="scss">
  @import url('https://fonts.googleapis.com/css2?family=Montserrat:wght@100;200;300;400;500;600;700;800;900&display=swap');

  $dark: #000000;
  $gray: #1F1F1F;


  #app {
    font-family: Avenir, Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
  }

  ::-webkit-scrollbar {
    width: 10px;
  }

  /* Track */
  ::-webkit-scrollbar-track {
    background: $dark;
  }

  /* Handle */
  ::-webkit-scrollbar-thumb {
    background: $gray;
  }

  /* Handle on hover */
  ::-webkit-scrollbar-thumb:hover {
    background: lighten($gray, 5%);
  }
</style>
