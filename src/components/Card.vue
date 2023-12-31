<template>
  <div class="card" :class="{ flipped: isFlipped }" @click="flipCard">
    <div class="back"></div>
    <div class="face">
      <!-- Apply cardSuitColor to the suit elements -->
      <div class="suit top" :class="cardSuitColor">{{ cardSuit }}</div>
      <div class="rank">{{ cardRank }}</div>
      <div class="suit bottom" :class="cardSuitColor">{{ cardSuit }}</div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    cardSuit: String,
    cardRank: String,
    isFlipped: Boolean,
  },
  /* data() {
    return {
      isFlipped: false, // Initially cards are face down
    };
  }, */
  methods: {
    flipCard() {
      this.$emit("flip");
    },
  },
  computed: {
    cardSuitColor() {
      return this.cardSuit === "♥" || this.cardSuit === "♦" ? "red" : "black";
    },
  },
};
</script>

<style scoped>
.card {
  width: 70px;
  height: 100px;
  margin-bottom: 0.25rem;
  background-color: #f8f8f8;
  transform-style: preserve-3d;
  transition: transform 1s;
  position: relative;
  border: none;
  padding: 0;
}

.card.flipped {
  transform: rotateY(180deg);
}

.face,
.back {
  position: absolute;
  width: 100%;
  height: 100%;
  backface-visibility: hidden;
}

.face {
  display: grid;
  grid-template-columns: 20px 1fr 20px;
  padding-left: 0.25rem;
  padding-right: 0.25rem;
  transform: rotateY(180deg);
}

.back {
  background-image: url("../assets/Reverso_baraja_española_rojo.svg");
  background-position: center;
  background-size: cover;
  transform: rotateY(0deg);
}

.suit {
  width: 100%;
  display: flex;
  flex-direction: column;
  column-gap: 0px;
}

.top {
  grid-column: 1;
  text-align: center;
}

.rank {
  align-self: center;
  text-align: center;
}

.bottom {
  grid-column: 3;
  text-align: center;
  transform: rotate(180deg);
}

.card .suit {
  color: inherit; /* or specify red/black explicitly */
}

.card .red {
  color: #ff0000;
}

.card .black {
  color: #000000;
}
</style>
