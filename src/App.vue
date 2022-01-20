<template>
  <div id="app">
    <div class="gameOver" v-if="gameOver">
      <h2>Congratulations! 👏</h2>
      <p>It took you <b>{{ clicks }}</b> clicks to finish</p>
      <button @click="resetGame">START AGAIN</button>
    </div>
    <p class="stats">Clicks: {{ clicks }}</p>
    <ul class="cards" :class="{ 'is-clickable': clickable }">
      <Card
        v-for="(card, index) in shuffled"
        :key="index"
        :card="card"
        @click.native="cardClick(card, index)"
        :class="[
          {
            'is-revealed':
              firstClickIndex === index || secondClickIndex === index
          },
          { 'is-found': found.includes(card) }
        ]"
      />
    </ul>
  </div>
</template>

<script>
import Card from './components/Card.vue'

export default {
  name: 'App',
  components: {
    Card
  },
  watch: {
    found() {
      if (this.found.length === this.cards.length) {
        this.gameOver = true
      }
    }
  },
  data() {
    return {
      cards: ['🐶', '🐱', '🐵', '🐼', '🐻', '🦊'],
      clicks: 0,
      firstClickIndex: null,
      secondClickIndex: null,
      timeout: null,
      clickable: true,
      found: [],
      gameOver: false
    }
  },
  computed: {
    pairs() {
      return [...this.cards, ...this.cards]
    },
    shuffled() {
      return this.pairs
        .map((a) => [Math.random(), a])
        .sort((a, b) => a[0] - b[0])
        .map((a) => a[1])
    }
  },
  methods: {
    cardClick(card, index) {
      this.clicks++
      const firstCard = this.shuffled[this.firstClickIndex]

      if (this.firstClickIndex === null) {
        this.firstClickIndex = index
      } else {
        this.secondClickIndex = index

        if (firstCard === card) {
          this.found.push(firstCard)
          this.firstClickIndex = null
          this.secondClickIndex = null
        } else {
          this.clickable = false
          setTimeout(() => {
            this.clickable = true
            this.firstClickIndex = null
            this.secondClickIndex = null
          }, 1000)
        }
      }
    },
    resetGame() {
      this.cards = [...this.cards]
      this.clicks = 0
      this.found = []
      this.gameOver = false
    }
  }
}
</script>

<style>
@import url("https://fonts.googleapis.com/css2?family=Poppins:ital,wght@0,100;0,200;0,300;0,400;0,500;0,600;0,700;0,800;0,900;1,100;1,200;1,300;1,400;1,500;1,600;1,700;1,800;1,900&display=swap");
* {
  margin: 0;
  padding: 0;
}
#app {
  font-family: 'Poppins';
  background-color: #D9D9D9;
  height: 100vh;
}
.cards {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  grid-gap: 20px;
  padding: 20px 0;
  max-width: 1000px;
  margin: 0 auto;
  pointer-events: none;
}
.cards.is-clickable {
  pointer-events: all;
}
.stats {
  position: absolute;
  top: 15px;
  right: 35px;
  font-size: 26px;
  font-weight: 500;
  color: #333;
  padding: 5px 12px;
  border-bottom: 2px solid #333;
}
.gameOver {
  position: fixed;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  z-index: 200;
  box-shadow: 0 10px 50px rgba(0, 0, 0, 0.4);
  padding: 60px 70px;
  background: white;
  color: #333;
  text-align: center;
  font-size: 18px;
  border-radius: 6px;
}
.gameOver h2 {
  margin: 0 0 20px 0;
}
.gameOver button {
  all: unset;
  margin-top: 30px;
  padding: 12px 32px;
  background: #333;
  color: white;
  border-radius: 6px;
  cursor: pointer;
  transition: 0.1s;
}
.gameOver button:hover {
  background: #404040;
}
</style>
