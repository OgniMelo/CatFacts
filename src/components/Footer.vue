<template>
  <footer>
    <p>Copyright Ogniter 'Melody' {{ date.getFullYear() }}</p>
  </footer>
</template>

<script setup>
import { ref } from 'vue'

const pouet = ref('Phrase de test de footer')
const date = ref(new Date())
</script>

<style lang="sass" scoped>
a
  color: #42b983
</style>