<template>
  <div class="header">
    <router-link :to="{ name: 'Home' }"><span>Coke</span></router-link>
    <router-link :to="{ name: 'Todos' }">Todos</router-link>
  </div>
  <div class="container">
    <router-view />
  </div>
</template>

<script>

  export default {
  }
</script>

<style>
  .header {
    display: flex;
    padding: 20px 50px;
  }

  span {
    margin-right: 30px;
  }

  .header div:first-child {
    font-weight: 500;
  }

  .container {
    padding: 0 50px
  }
</style>
