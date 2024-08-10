<template>
  <Menu>
    <router-link to="/">Home</router-link> |
    <router-link to="/about">About</router-link>
    <router-link to="/tipos">Tipos</router-link>
    <router-link to="/ganados">Ganados</router-link>
  </Menu>
  <router-view/>
</template>

<script>

import Menu from './components/Menu.vue'

export default {
  name: 'App',
  data(){
    return {

    }
  },
  components:{
    Menu
  }
}
</script>

<style lang="scss">

</style>
