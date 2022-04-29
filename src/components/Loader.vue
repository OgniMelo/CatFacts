<template>
  <div id="loaderdiv">
    <div id="loader"></div>
  </div>
</template>

<style lang="sass">
#loaderdiv
  min-height: 100vh

#loader
  display: block
  position: fixed
  top: 50%
  left: 50%
  height: 50px
  width: 50px
  margin: -25px 0 0 -25px
  border: 8px rgba(0, 0, 0, 0) solid
  border-top: 8px var(--mainColor) solid
  border-right: 8px var(--mainColor) solid
  border-bottom: 8px var(--mainColor) solid
  border-radius: 50%
  animation: spin 1s infinite linear

@keyframes spin
  from
    transform: rotate(0deg)
  to
    transform: rotate(359deg)
</style>