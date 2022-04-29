<template>
  <Loader v-if="state.loading" />
  <main v-if="state.cats && state.cats.length && !state.error">
    <div class="cat" v-for="cat of state.cats" :key="cat">
      <img src="../assets/potichat.png" alt="Potichat tout mignon" title="Potichat tout mignon">
      <p>Breed: {{ cat.breed }}</p>
      <p>Country: {{ cat.country || 'Unknown' }} <img v-if="cat.flag" :src="cat.flag" alt="Country flag"></p>
      <p>Origin: {{ cat.origin || 'Unknown' }}</p>
      <p>Coat: {{ cat.coat || 'Unknown' }}</p>
      <p>Pattern: {{ cat.pattern || 'Unknown' }}</p>
    </div>
  </main>
  <main v-if="!state.loading && state.error">
    <p class="error">{{ state.error }}</p>
  </main>
</template>

<script setup>
import Loader from '../components/Loader.vue'
import { reactive, onBeforeMount } from 'vue'

const state = reactive({
  loading: false,
  error: '',
  page: 1,
  lastPage: null,
  cats: []
})

const getCatsInfo = async () => {
  try {
    state.loading = true
    const reqSettings = {
      headers: {
        accept: 'application/json'
      }
    }
    const response = await fetch('https://catfact.ninja/breeds?page=' + state.page, reqSettings)
    const data = await response.json()
    state.lastPage = data.last_page
    for (const cat of data.data) {
      if (!cat.country) {
        continue
      }
      const countryRes = await fetch('https://restcountries.com/v3.1/name/' + cat.country)
      if (countryRes.ok) {
        const countryData = await countryRes.json()
        cat.flag = countryData[0].flags.svg
      }
    }
    state.cats = state.cats.concat(data.data)
    console.log(state.cats)
    state.loading = false
    state.error = ''
  }
  catch {
    state.loading = false
    state.error = 'Error when retrieving cats breeds, try again later'
  }
}

onBeforeMount(async () => {
  window.onscroll = async () => {
    if (state.loading) {
      return
    }

    if ((window.innerHeight + window.scrollY) >= document.body.offsetHeight) {
      if (state.lastPage && state.page < state.lastPage) {
        state.page++
        await getCatsInfo()
        return
      }
    }
  }

  await getCatsInfo()
})
</script>

<style lang="sass" scoped>
main
  min-height: 100vh
  max-width: 95%
  margin: auto
  display: flex
  flex-direction: column
  row-gap: 25px

.cat
  margin: auto
  width: 95%
  background-color: #eee
  cursor: pointer
  border: 1px var(--mainColor) solid
  border-radius: 10px

  > img
    width: 50px
    height: 75px

  p:nth-child(2)
    margin-top: 0

  > p
    img
      width: 30px
      height: 20px

@media screen and (min-width: 769px)
  main
    max-width: 75%
    flex-direction: row
    flex-wrap: wrap

  .cat
    margin: 0 calc(.75% - 2px)
    width: 23.5%
</style>