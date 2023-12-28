<script>
import Card from './components/Card.vue';
import flipCard from './components/Card.vue';
export default {
  components: {
    Card
  },
  data() {
    return {
      ranks: ['A', '2', '3', '4', '5', '6', '7', '8', '9', '10', 'J', 'Q', 'K'],
      suits: ['♥', '♦', '♠', '♣'],
      cards: [],
      p1: [],
      p2: [],
      p1score: 0,
      p2score: 0,
      dealbutton: 0,
      hitbutton: 0,
      standbutton: 0,
      shufflebutton: 1,
      suitColor: {
        '♥': 'red',
        '♦': 'red',
        '♣': 'black',
        '♠': 'black',
      },
      shuffleSpeed: 'shuffleMedium',
    }
  },
  created() {
    this.displayInitialDeck();
  },
  methods: {
    displayInitialDeck() {
      let id = 1;
      for (let s = 0; s < 4; s++) {
        for (let r = 0; r < 13; r++) {
          let card = {
            id: id,
            rank: this.ranks[r],
            suit: this.suits[s],
          }
          this.cards.push(card);
          id++;
        }
      }
    },
    shuffleDeck() {
      this.dealbutton = 1;
      for (let i = this.cards.length - 1; i > 0; i--) {
        let randomIndex = Math.floor(Math.random() * i);
        let temp = this.cards[i];
        this.cards[i] = this.cards[randomIndex];
        this.cards[randomIndex] = temp;
      }
      for (let i = 0; i < this.cards.length - 1; i++) {
        console.out(this.cards[i].rank.this.cards[i].suit)
      }

    },
    deal() {
      this.dealbutton = 0;
      this.p1.push(this.cards.shift());
      this.p2.push(this.cards.shift());
      this.p1.push(this.cards.shift());
      this.p2.push(this.cards.shift());
      this.score();
      this.hitbutton = 1;
      this.standbutton = 1;
      this.shufflebutton = 0;
      if (this.p1score == 21) {
        this.p1score = "Blackjack! ";
        this.hitbutton = 0;
        this.standbutton = 0;
      } else {
        this.score();
      }
    },
    hit() {
      this.p1.push(this.cards.shift());
      this.score();
      if (this.p1score > 21) {
        this.p1score = "Busted! ";
        this.hitbutton = 0;
        this.standbutton = 0;
      } else if (this.p1score == 21) {
        this.stand();
      } else {
        this.score();
      }
    },
    stand() {
      this.hitbutton = 0;
      this.standbutton = 0;
      this.dealersTurn();
    },
    dealersTurn() {
      this.score();
      if (this.p2score == this.p1score) {
        this.p1score = "Draw! ";
      } else if (this.p2score > this.p1score && this.p2score < 22) {
        this.p1score = "Lose! Dealer has " + this.p2score;
      } else if (this.p2score > 21) {
        this.p1score = "Win! Dealer busted ";
      } else {
        this.p2.push(this.cards.shift());
        this.score();
        this.dealersTurn();
      }
    },
    restart() {
      while (this.p1.length > 0) {
        this.cards.push(this.p1.shift());
      }
      while (this.p2.length > 0) {
        this.cards.push(this.p2.shift());
      }
      this.p1score = 0;
      this.p2score = 0;
      this.standbutton = 0;
      this.hitbutton = 0;
      this.shufflebutton = 1;
    },
    score() {
      let myscore = 0;
      this.p1.forEach(function (card) {
        switch (card.rank) {
          case '2': myscore += 2; break;
          case '3': myscore += 3; break;
          case '4': myscore += 4; break;
          case '5': myscore += 5; break;
          case '6': myscore += 6; break;
          case '7': myscore += 7; break;
          case '8': myscore += 8; break;
          case '9': myscore += 9; break;
          case '10': myscore += 10; break;
          case 'J': myscore += 10; break;
          case 'K': myscore += 10; break;
          case 'Q': myscore += 10; break;
          case 'A':
            if (myscore > 10) {
              myscore = myscore + 1;
            } else {
              myscore += 11; break;
            }

        }
      })
      let dealerscore = 0;
      this.p2.forEach(function (card) {
        switch (card.rank) {
          case '2': dealerscore += 2; break;
          case '3': dealerscore += 3; break;
          case '4': dealerscore += 4; break;
          case '5': dealerscore += 5; break;
          case '6': dealerscore += 6; break;
          case '7': dealerscore += 7; break;
          case '8': dealerscore += 8; break;
          case '9': dealerscore += 9; break;
          case '10': dealerscore += 10; break;
          case 'J': dealerscore += 10; break;
          case 'K': dealerscore += 10; break;
          case 'Q': dealerscore += 10; break;
          case 'A':
            if (dealerscore > 10) {
              dealerscore = dealerscore + 1;
            } else {
              dealerscore += 11; break;
            }

        }
      })
      this.p1score = myscore;
      this.p2score = dealerscore;

    },

  }
}
</script>

<template>

  <button v-if="shufflebutton" @click="shuffleDeck">Shuffle</button>
  <button v-if="dealbutton" @click="deal">Deal</button>
  <button v-show="hitbutton" @click="hit">Hit</button>
  <button v-if="standbutton" @click="stand">Stand</button>
  <button @click="restart">Restart</button>
  <div id="player1">
    <div class="player score">
      Player: {{p1score}}
    </div>
    <transition-group name="p1cards" appear>
      <div v-for="card in p1" :key="card.id" class="player" :class=suitColor[card.suit]>
        <Card :card-rank="card.rank" :card-suit="card.suit" />
      </div>
    </transition-group>
  </div>

  <div id="player2">
    <div class="player score">
      Dealer:
    </div>
    <transition-group name="p1cards" appear>
      <div v-for="card in p2" :key="card.id" class="player" :class=suitColor[card.suit]>
        <Card :card-rank="card.rank" :card-suit="card.suit" />
      </div>
    </transition-group>
  </div>
  <div id="deck">
    <transition-group :name="shuffleSpeed" tag="div" class="felt">
      <div v-for="card in cards" :key="card.id" class="deck" :class=suitColor[card.suit]>
        <Card :card-rank="card.rank" :card-suit="card.suit" />
      </div>
    </transition-group>
  </div>
</template>

<style scoped>
header {
  line-height: 1.5;
}

/* .fade-enter-active,
.fade-leave-active {
  transition: opacity .5s;
}

.fade-enter,
.fade-leave-to {
  opacity: 0;
} */

.p1cards-enter-from {
  transform: translateX(150px);
}

.p1cards-enter-to {
  transition: transform 2s;
}



.shuffleMedium-move {
  transition: transform 1s;
}

.logo {
  display: block;
  margin: 0 auto 2rem;
}

.player {
  float: left;
}

#player1,
#player2,
#deck {
  clear: both;
}

.deck {
  float: left;
  margin-right: .25em;
  margin-bottom: .25em;
}

p {
  color: blue;
}

.red {
  color: #FF0000;
}

.blue {
  color: #0000FF;
}

.green {
  color: #00ff00;
}

.black {
  color: #000000;
}

@media (min-width: 1024px) {
  header {
    display: flex;
    place-items: center;
    padding-right: calc(var(--section-gap) / 2);
  }

  .logo {
    margin: 0 2rem 0 0;
  }

  header .wrapper {
    display: flex;
    place-items: flex-start;
    flex-wrap: wrap;
  }
}
</style>
