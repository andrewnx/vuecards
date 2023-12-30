<template>
  <!-- Existing UI elements -->
  <button v-if="shufflebutton" @click="shuffleDeck">Shuffle</button>

  <!-- Game Mode Selection -->
  <button @click="setGameMode('blackjack')">Blackjack Mode</button>
  <button @click="setGameMode('poker')">Poker Mode</button>

  <!-- Deal Buttons -->
  <template v-for="count in 7">
    <button @click="dealCards(count, 'player')">
      Deal {{ count }}: Player
    </button>
    <button @click="dealCards(count, 'dealer')">
      Deal {{ count }}: Dealer
    </button>
  </template>

  <button @click="flipPlayerCards">Flip Player Cards</button>
  <button @click="flipDealerCards">Flip Dealer Cards</button>
  <button @click="flipAllCards">Flip All Cards</button>

  <button @click="restart">Restart</button>

  <div id="player1" class="player-area">
    <div class="score">Player:</div>
    <div class="cards-container">
      <Card
        v-for="card in p1"
        :key="card.id"
        :card-rank="card.rank"
        :card-suit="card.suit"
      />
    </div>
  </div>

  <div id="player2" class="player-area">
    <div class="score">Dealer:</div>
    <div class="cards-container">
      <Card
        v-for="card in p2"
        :key="card.id"
        :card-rank="card.rank"
        :card-suit="card.suit"
      />
    </div>
  </div>

  <div id="deck" class="deck-area" :style="{ height: deckHeight + 'px' }">
    <div class="cards-container">
      <Card
        v-for="card in cards"
        :key="card.id"
        :card-rank="card.rank"
        :card-suit="card.suit"
        :class="getCardColor(card.suit)"
      />
    </div>
  </div>
</template>

<script>
import Card from "./components/Card.vue";

export default {
  components: {
    Card,
  },
  data() {
    return {
      ranks: ["A", "2", "3", "4", "5", "6", "7", "8", "9", "10", "J", "Q", "K"],
      suits: ["♥", "♦", "♠", "♣"],
      cards: [],
      p1: [],
      p2: [],
      dealbutton: 0,
      shufflebutton: 1,
      suitColor: {
        "♥": "red",
        "♦": "red",
        "♣": "black",
        "♠": "black",
      },
      shuffleSpeed: "shuffleMedium",
      currentMode: null,
    };
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
          };
          this.cards.push(card);
          id++;
        }
      }
    },
    shuffleDeck() {
      this.dealbutton = 1;
      for (let i = this.cards.length - 1; i > 0; i--) {
        let randomIndex = Math.floor(Math.random() * (i + 1));
        let temp = this.cards[i];
        this.cards[i] = this.cards[randomIndex];
        this.cards[randomIndex] = temp;
      }
    },
    dealCards(count, recipient) {
      for (let i = 0; i < count; i++) {
        if (this.cards.length > 0) {
          const card = this.cards.shift();
          if (recipient === "player") {
            this.p1.push(card);
          } else if (recipient === "dealer") {
            this.p2.push(card);
          }
        }
      }
    },
    setGameMode(mode) {
      this.currentMode = mode;
    },
    restart() {
      this.cards = [];
      this.p1 = [];
      this.p2 = [];
      this.displayInitialDeck();
      this.shufflebutton = 1;
    },
    flipCard(card) {
      card.faceUp = !card.faceUp;
    },
    getCardColor(suit) {
      return this.suitColor[suit];
    },
    flipPlayerCards() {
      this.p1.forEach((card) => {
        card.isFlipped = !card.isFlipped;
      });
    },
    flipDealerCards() {
      this.p2.forEach((card) => {
        card.isFlipped = !card.isFlipped;
      });
    },
    flipAllCards() {
      this.flipPlayerCards();
      this.flipDealerCards();
    },
  },
  computed: {
    deckHeight() {
      return 500 + (this.cards.length - 1) * 5;
    },
  },
};
</script>

<style scoped>
/* General Styles */
header {
  line-height: 1.5;
}

.logo {
  display: block;
  margin: 0 auto 2rem;
}

/* Player, Dealer, and Deck Styles */
#player1,
#player2,
#deck {
  margin: 1rem 0;
  padding: 1rem;
  border: 1px solid var(--color-border);
  border-radius: 5px;
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  align-items: flex-start;
}

.player .card,
.dealer .card,
.deck .card {
  flex: 0 1 calc(33.333% - 1rem);
}

#player1 {
  background-color: #e0f7fa;
  color: var(--vt-c-black);
}

#player2 {
  background-color: #455a64;
  color: var(--vt-c-black);
}

#deck {
  background-color: #e8f5e9;
  position: relative;
  height: 150px;
}

/* Score Style */
.score {
  font-weight: bold;
  margin-bottom: 0.5rem;
}

/* Color Classes */
.red {
  color: #ff0000;
}

.blue {
  color: #0000ff;
}

.green {
  color: #00ff00;
}

.black {
  color: #000000;
}

/* Media Queries */
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
@media (max-width: 600px) {
  .player .card,
  .dealer .card,
  .deck .card {
    flex: 0 1 calc(50% - 1rem);
  }
}

@media (min-width: 1024px) {
  .player .card,
  .dealer .card,
  .deck .card {
    flex: 0 1 calc(25% - 1rem);
  }
}

/* Animations and Transitions */
.shuffleMedium-move {
  transition: transform 1s;
}

.p1cards-enter-from {
  transform: translateX(150px);
}

.p1cards-enter-to {
  transition: transform 2s;
}

.player-area,
.deck-area {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.cards-container {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
}

.deck-container {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
}

.deck-area .cards-container {
  position: relative;
  width: 100%;
  height: 100%;
}
</style>
