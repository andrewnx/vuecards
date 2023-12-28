<template>
  <div class="card" @click="flipCard">
    <div class="back"></div>
    <div class="face" v-if="isFlipped">
      <div class="suit top">{{ cardSuit }}</div>
      <div class="rank">{{ cardRank }}</div>
      <div class="suit bottom">{{ cardSuit }}</div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    cardSuit: String,
    cardRank: String,
  },
  data() {
    return {
      isFlipped: false, // Initially cards are face down
    };
  },
  methods: {
    flipCard() {
      this.isFlipped = !this.isFlipped;
    },
  },
};
</script>

<style scoped>
.card {
  border-radius: 5px;
  border-width: 1px;
  border-style: solid;
  border-color: black;
  width: 70px;
  height: 100px;
  margin-bottom: 0.25rem;
  background-color: #f8f8f8;
  transform-style: preserve-3d;
  transition: transform 1s;
  position: relative;
}

.card.flipped {
  transform: rotateY(180deg);
}

.face,
.back {
  width: 100%;
  height: 100%;
  position: absolute;
  backface-visibility: hidden;
}

.face {
  display: grid;
  grid-template-columns: 20px 1fr 20px;
  padding-left: 0.25rem;
  padding-right: 0.25rem;
}

.back {
  background-color: blue; /* Change as needed */
  background-image: url("../assets/Reverso_baraja_española_rojo.svg"); /* Update with your back image */
  background-position: center;
  background-size: cover;
  transform: rotateY(180deg);
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
</style>
