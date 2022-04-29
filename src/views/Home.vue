<template>
  <Loader v-if="state.loading" />
  <main v-if="state.cats && state.cats.length && !state.error">
    <div id="modalBg" v-if="state.showFact" @click="state.showFact = false"></div>
    <div id="modal" v-if="state.showFact">
      <h2>Random Fact</h2>
      <p>{{ state.catFact }}</p>
      <button @click="state.showFact = false">Close</button>
    </div>
    <div class="cat" v-for="cat of state.cats" :key="cat" @click="getCatFact()">
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
  cats: [],
  showFact: false,
  catFact: 'null'
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
    if (response.ok) {
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
      state.loading = false
      state.error = ''
      return
    }
    state.loading = false
    state.error = 'Error when retrieving cats breeds, try again later'
  }
  catch {
    state.loading = false
    state.error = 'Error when retrieving cats breeds, try again later'
  }
}

const getCatFact = async () => {
  try {
    state.loading = false
    const reqSettings = {
      headers: {
        accept: 'application/json'
      }
    }
    const response = await fetch('https://catfact.ninja/fact', reqSettings)
    if (response.ok) {
      state.showFact = true
      const data = await response.json()
      state.loading = false
      state.catFact = data.fact
      return
    }
    state.loading = false
    state.catFact = 'Error when retrieving a cat fact, try again later.'
  }
  catch {
    state.loading = false
    state.catFact = ''
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
  justify-content: space-around
  align-items: space-around
  row-gap: 25px

#modalBg
  z-index: 2
  position: fixed
  top: 0
  left: 0
  width: 100%
  height: 100%
  background-color: #0000007F

#modal
  z-index: 3
  position: fixed
  top: 50%
  left: 50%
  width: 95%
  margin: auto
  padding: 1em 0
  transform: translate(-50%, -50%)
  border-radius: 10px
  background-color: white

  button
    height: 30px
    border-radius: 25px
    outline: none
    border: none
    cursor: pointer
    background-color: var(--mainColor)
    filter: brightness(1.2)

.cat
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
    flex-direction: row
    flex-wrap: wrap

  #modal
    width: 75%

  .cat
    width: 45%

@media screen and (min-width: 993px)
  main
    max-width: 75%
    flex-direction: row
    flex-wrap: wrap

  #modal
    width: 50%

  .cat
    width: 22.5%
</style>