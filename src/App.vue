<template>
  <button v-if="shufflebutton" @click="shuffleDeck" class="primary-action">
    Shuffle
  </button>
  <button
    v-if="!isBlackjack && !isPoker"
    @click="setGameMode('blackjack')"
    class="primary-action"
  >
    Blackjack Mode
  </button>
  <button
    v-if="!isBlackjack && !isPoker"
    @click="setGameMode('poker')"
    class="primary-action"
  >
    Poker Mode
  </button>

  <div class="button-container">
    <!-- Basic Mode buttons -->
    <template v-if="!isBlackjack && !isPoker">
      <template v-for="count in 7">
        <button @click="dealCards(count, 'player')">
          Deal {{ count }}: Player
        </button>
        <button @click="dealCards(count, 'dealer')">
          Deal {{ count }}: Dealer
        </button>
      </template>
      <button @click="flipAllCards">Flip All Cards</button>
    </template>

    <!-- Blackjack Mode buttons -->
    <button @click="playerHit" v-if="isBlackjack && !playerStand">Hit</button>
    <button @click="playerStand" v-if="isBlackjack && !playerStand">
      Stand
    </button>
    <button @click="restart">Restart</button>
  </div>

  <div id="player1" class="player-area">
    <div class="score">Player: {{ playerTotal }}</div>
    <div class="cards-container">
      <Card
        v-for="card in p1"
        :key="card.id"
        :card-rank="card.rank"
        :card-suit="card.suit"
        :is-flipped="card.isFlipped"
      />
    </div>
  </div>

  <div id="player2" class="player-area">
    <div class="score">Dealer: {{ dealerTotal }}</div>
    <div class="cards-container">
      <Card
        v-for="card in p2"
        :key="card.id"
        :card-rank="card.rank"
        :card-suit="card.suit"
        :is-flipped="card.isFlipped"
      />
    </div>
  </div>

  <div id="deck" class="deck-area" :class="{ 'is-shuffling': isShuffling }">
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
  components: { Card },
  data() {
    return {
      ranks: ["A", "2", "3", "4", "5", "6", "7", "8", "9", "10", "J", "Q", "K"],
      suits: ["♥", "♦", "♠", "♣"],
      suitColor: { "♥": "red", "♦": "red", "♣": "black", "♠": "black" },
      cards: [],
      p1: [],
      p2: [],
      shufflebutton: true,
      isShuffling: false,
      playerTotal: 0,
      dealerTotal: 0,
      isBlackjack: false,
      isPoker: false,
      playerStand: false,
    };
  },
  created() {
    this.initializeDeck();
  },
  methods: {
    initializeDeck() {
      this.cards = [];
      for (let s = 0; s < this.suits.length; s++) {
        for (let r = 0; r < this.ranks.length; r++) {
          this.cards.push({
            id: this.cards.length + 1,
            rank: this.ranks[r],
            suit: this.suits[s],
            isFlipped: false,
          });
        }
      }
    },
    shuffleDeck() {
      this.isShuffling = true;
      let currentIndex = this.cards.length,
        temporaryValue,
        randomIndex;
      while (0 !== currentIndex) {
        randomIndex = Math.floor(Math.random() * currentIndex);
        currentIndex -= 1;
        temporaryValue = this.cards[currentIndex];
        this.cards[currentIndex] = this.cards[randomIndex];
        this.cards[randomIndex] = temporaryValue;
      }
      setTimeout(() => {
        this.isShuffling = false;
        this.shufflebutton = false;
      }, 1000);
    },
    dealCards(count, recipient) {
      for (let i = 0; i < count; i++) {
        if (this.cards.length) {
          const card = this.cards.shift();
          if (recipient === "player") {
            this.p1.push(card);
          } else {
            this.p2.push(card);
            if (this.p2.length === 2) {
              card.isFlipped = true; // Second dealer card is face down
            }
          }
        }
      }
      this.playerTotal = this.calculateTotal(this.p1);
      this.dealerTotal = this.calculateTotal(
        this.p2.filter((c) => !c.isFlipped)
      );
    },
    calculateTotal(hand) {
      let total = hand.reduce((acc, card) => {
        if (card.rank === "A") {
          return acc + 11; // Value of Ace is 11 (temporarily)
        } else if (["J", "Q", "K"].includes(card.rank)) {
          return acc + 10; // Face cards are 10
        } else {
          return acc + parseInt(card.rank);
        }
      }, 0);

      // Adjust for Aces if total is over 21
      hand.forEach((card) => {
        if (card.rank === "A" && total > 21) total -= 10;
      });

      return total;
    },
    setGameMode(mode) {
      this.restart();
      if (mode === "blackjack") {
        this.isBlackjack = true;
        this.shuffleDeck();
        setTimeout(() => {
          this.dealCards(2, "player");
          this.dealCards(2, "dealer");
        }, 1000);
      }
    },
    playerHit() {
      if (this.playerTotal < 21 && !this.playerStand) {
        this.dealCards(1, "player");
      }
      if (this.playerTotal > 21) {
        alert("Player busts!"); // Placeholder for player bust logic
      }
    },
    dealerTurn() {
      this.dealerTotal = this.calculateTotal(this.p2);
      while (this.dealerTotal < 17) {
        this.dealCards(1, "dealer");
        this.dealerTotal = this.calculateTotal(this.p2);
      }
      this.endGame();
    },
    endGame() {
      if (this.playerTotal > 21) {
        alert("Player busts!");
      } else if (this.dealerTotal > 21) {
        alert("Dealer busts! Player wins!");
      } else if (this.playerTotal === this.dealerTotal) {
        alert("Push!");
      } else if (this.playerTotal > this.dealerTotal) {
        alert("Player wins!");
      } else {
        alert("Dealer wins!");
      }
      this.restart(); // Prepare for a new game
    },
    restart() {
      this.initializeDeck();
      this.p1 = [];
      this.p2 = [];
      this.shufflebutton = true;
      this.isBlackjack = false;
      this.playerStand = false;
    },
    getCardColor(suit) {
      return this.suitColor[suit];
    },
  },
  computed: {
    deckHeight() {
      return this.cards.length * 0.5 + "rem";
    },
  },
};
</script>

<style scoped>
/* Base layout styles */
header {
  line-height: 1.5;
}

.logo {
  display: block;
  margin: 0 auto 2rem;
}

/* Player and deck styles */
#player1,
#player2,
#deck {
  margin: 1rem auto;
  padding: 1rem;
  border: 1px solid var(--color-border);
  border-radius: 5px;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 1rem;
  color: black;
}

/* Deck specific styles */
#deck {
  background-color: var(--base02); /* Solarized selection background */
}

/* Score and button styling */
.score {
  font-weight: bold;
  margin-bottom: 0.5rem;
  color: var(--color-heading);
}

button {
  padding: 0.5rem 1rem;
  border-radius: 4px;
  background-color: var(--color-background-mute);
  color: var(--color-text);
  border: 1px solid var(--color-border);
  flex: 1;
  margin: 5px;
  min-width: 120px;
  text-align: center;
  cursor: pointer;
  transition: background-color 0.3s, transform 0.2s;
}

button:hover,
button:focus {
  background-color: var(
    --bright-accent
  ); /* Electric Blue for hover background */
  color: var(--dark-background); /* Gunmetal for text, ensures contrast */
}

/* Primary action buttons */
.primary-action {
  background-color: var(--accent-color); /* Teal */
  color: var(--color-background); /* Gunmetal for text */
  padding: 0.75rem 1.5rem;
  font-size: 1.1rem;
  font-weight: bold;
  border: none;
  margin: 0 5px 10px;
}

.primary-action:hover,
.primary-action:focus {
  background-color: var(--bright-accent); /* Electric Blue */
  color: var(--color-background-mute);
  transform: translateY(-2px);
}

/* Card styles */
.cards-container {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  gap: 10px;
}

#player1 .card,
#player2 .card,
#deck .card {
  margin: 3px;
}

/* Animation for shuffling */
@keyframes shuffleAnimation {
  0%,
  100% {
    transform: rotate(0deg);
  }
  50% {
    transform: rotate(20deg);
  }
}

.deck-area.is-shuffling .card {
  animation: shuffleAnimation 1s ease-in-out;
}

/* Media queries for responsive design */
@media (min-width: 1024px) {
  .player .card,
  .dealer .card,
  .deck .card {
    flex: 0 1 calc(25% - 10px);
  }

  .button-container {
    display: flex;
    flex-wrap: wrap;
    justify-content: center;
    gap: 10px;
  }

  button {
    min-width: 150px;
    margin: 10px;
  }
}

@media (max-width: 600px) {
  .player .card,
  .dealer .card,
  .deck .card {
    flex: 0 1 calc(50% - 10px);
  }
}
</style>
