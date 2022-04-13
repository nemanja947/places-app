<template>
  <div id="app">
    <nav>
      <router-link to="/">Home</router-link> 
      <router-link :to="`/places/${place.id}`" v-for="place in availablePlaces" :key="place.id">{{place.name}}</router-link> 
    </nav>
    <router-view/>
  </div>
</template>

<script>
export default {
  data() {
    return {
      availablePlaces: [
        {
          id: 'ohGSnJtMIC5nPfYRi_HTAg',
          name: 'Le Café du Marché'
        },
        {
          id: 'GXvPAor1ifNfpF0U5PTG0w',
          name: 'Casa Ferlin'
        }
      ]
    }
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

nav {
  padding: 30px;
}

nav a {
  font-weight: bold;
  color: #2c3e50;
  margin-right: 16px;
  text-decoration: none;
}

nav a.router-link-exact-active {
  color: #42b983;
}
</style>
