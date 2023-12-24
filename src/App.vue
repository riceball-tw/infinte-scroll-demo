<script setup>
import { ref } from "vue"
import { throttle } from 'lodash'
import Observer from "./components/Observer.vue"

const infinteScrollOptions = {
  maxPage: 10,
  limitsPerPage: 3,
  currentPage: 1,
  isInView: false
}
const cards = ref(getCards(infinteScrollOptions.limitsPerPage, infinteScrollOptions.currentPage))

const handleLoadmore  = throttle(function(options = infinteScrollOptions) {
  const { limitsPerPage, currentPage, isInView, maxPage } = options
  if (currentPage > maxPage) return
  const newCurrentPage = currentPage + 1
  infinteScrollOptions.currentPage = newCurrentPage
  const newCards = getCards(limitsPerPage, newCurrentPage)
  cards.value = [...cards.value, ...newCards];
  if (isInView) {
    handleLoadmore()
  }
}, 300, { leading: true, trailing: true })
	
function handleIsInView() {
  infinteScrollOptions.isInView = true
  handleLoadmore()
}

function handleIsOutsideView() {
  infinteScrollOptions.isInView = false
}

function getCards(limitsPerPage, currentPage) {
  return Array.from({ length: limitsPerPage }, () => ({ title: currentPage.toString() }));
}
</script>

<template>
  <div id="app">
		<div class="container">
			<h1 class="title">Infinte Scroll Vue</h1>
			<button @click="handleLoadmore">Click to Load</button>
      <ul class="cards">
        <li class="card" v-for="(card, index) in cards">
          <img :src="`https://picsum.photos/id/${index}/300/300`" width="300" height="300"  alt="Random Image">
          <p>
            #Image: {{ index }}
          </p>
        </li>
      </ul>
      <Observer
       v-if="!(infinteScrollOptions.currentPage > infinteScrollOptions.maxPage)" 
       @is-in-view="handleIsInView"
        @is-outside-view="handleIsOutsideView" />
		</div>
  </div>
</template>

<style>
:\root {
  --vue: #42b883; 
  --card-bg: #35495e;
}

body {
  background: var(--vue);
  color: white; }

.title {
  font-size: 4rem;
  font-weight: bold;
  margin: 2rem 0; }

.container {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  min-height: 100vh; }

.cards {
  text-align: center;
  list-style-type: none;
  margin: 0;
  padding: 2rem;
  display: grid;
  width: 100%;
  max-width: 1200px;
  grid-template-columns: repeat( auto-fit, minmax(300px, 1fr) );
  flex-direction: column;
  gap: 1rem; }

.card {
  padding: 1rem;
  background: var(--card-bg); }

</style>