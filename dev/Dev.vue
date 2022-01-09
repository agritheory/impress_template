<script setup>
import Presentation from "./../src/components/Presentation.vue"

const slides = `

---
x: 0
y: 0
rotate: 0
scale: 1
config: classicConfig
---

Aren't you just __bored__ with all those slides-based presentations?

---
x: 485
y: 0
rotate: 0
scale: 0.5
config: classicConfig

---

Don't you think that presentations given in __modern browsers__ shouldn't __copy the limits__ of 'classic' slide decks?

---
x: 970
y: 0
rotate: 0
scale: 0.5
config: classicConfig

---

Would you like to __impress your audience__ with __stunning visualization__ of your talk?

---
x: 485
y: 700
rotate: 0,
scale: 1.5
config: modernConfig

---

maybe you have already tried

# [impress.js](https://github.com/impress/impress.js)
`


</script>

<template>
  <presentation
    :deck="slides"
  ></presentation>
</template>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
