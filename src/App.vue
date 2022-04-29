<template>
  <Header />
  <Home />
  <Footer />
</template>

<script>
import Header from './components/Header.vue'
import Home from './views/Home.vue'
import Footer from './components/Footer.vue'

export default {
  components: {
    Header,
    Home,
    Footer
  }
}
</script>

<style lang="sass">
:root
  --mainColor: hsl(199, 60%, 60%)

body
  margin: 0

#app
  font-family: Avenir, Helvetica, Arial, sans-serif
  -webkit-font-smoothing: antialiased
  -moz-osx-font-smoothing: grayscale
  text-align: center
  color: #2c3e50

.error
  color: red
  font-weight: bold
</style>
