<template>
  <div class="display">
    <h1>O'yin maydoni</h1>
    <transition-group tag="section" name="shuffle-card" class="game-board">
      <Card
        v-for="(card, index) in cardList"
        :key="`card-${index}`"
        :matched="card.matched"
        :value="card.value"
        :visible="card.visible"
        :position="card.position"
        @select-card="flipCard"
      />
    </transition-group>
    <br />
    <span> {{ status }} </span> <br />
    <button @click="restartGame">
      <img src="/images/restart.svg" alt="Restart Icon" /> Qayta boshlash
    </button>
  </div>
</template>

<script>
import _ from "lodash";
import { computed, ref, watch } from "vue";
import Card from "./components/Card";

export default {
  name: "App",
  components: {
    Card,
  },
  setup() {
    const cardList = ref([]);
    const userSelection = ref([]);

    const status = computed(() => {
      if (remainingPairs.value === 0) {
        return "G'alaba!";
      } else {
        return `Qadamlar soni: ${remainingPairs.value}`;
      }
    });

    const remainingPairs = computed(() => {
      const remainingPairs = cardList.value.filter(
        (card) => card.matched === false
      ).length;

      return remainingPairs / 2;
    });

    const shuffleCards = () => {
      cardList.value = _.shuffle(cardList.value);
    };

    const restartGame = () => {
      shuffleCards();

      cardList.value = cardList.value.map((card, index) => {
        return {
          ...card,
          matched: false,
          position: index,
          visible: false,
        };
      });
    };
    const cardItems = [
      "bat",
      "candy",
      "cauldron",
      "cupcake",
      "ghost",
      "moon",
      "pumpkin",
      "witch-hat",
    ];

    cardItems.forEach((item) => {
      cardList.value.push({
        value: item,
        visible: false,
        position: null,
        matched: false,
      });

      cardList.value.push({
        value: item,
        visible: false,
        position: null,
        matched: false,
      });
    });

    cardList.value = cardList.value.map((card, index) => {
      return {
        ...card,
        position: index,
      };
    });

    const flipCard = (payload) => {
      cardList.value[payload.position].visible = true;

      if (userSelection.value[0]) {
        if (
          userSelection.value[0] === payload.position &&
          userSelection.value[0].faceValue === payload.faceValue
        ) {
          return;
        } else {
          userSelection.value[1] = payload;
        }
      } else {
        userSelection.value[0] = payload;
      }
    };

    watch(
      userSelection,
      (currentValue) => {
        if (currentValue.length === 2) {
          const cardOne = currentValue[0];
          const cardTwo = currentValue[1];

          if (cardOne.faceValue === cardTwo.faceValue) {
            cardList.value[cardOne.position].matched = true;
            cardList.value[cardTwo.position].matched = true;
          } else {
            setTimeout(() => {
              cardList.value[cardOne.position].visible = false;
              cardList.value[cardTwo.position].visible = false;
            }, 2000);
          }

          userSelection.value.length = 0;
        }
      },
      { deep: true }
    );

    return {
      cardList,
      flipCard,
      userSelection,
      status,
      shuffleCards,
      restartGame,
    };
  },
};
</script>

<style>
html,
body {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

.display {
  width: 100vw;
  height: 100vh;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}


#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  background-image: url("/images/page-bg.png");
  background-color: #00070c;
  height: 100vh;
  color: #fff;
}

.game-board {
  display: grid;
  grid-template-columns: repeat(4, 110px);
  grid-template-rows: repeat(4, 110px);
  grid-column-gap: 10px;
  grid-row-gap: 10px;
  justify-content: center;
}

@media (max-width: 560px) {
  .game-board {
    grid-template-columns: repeat(4, 80px);
    grid-template-rows: repeat(4, 80px);
    padding: 50px 0;
  }
}

button {
  background: orange;
  color: #fff;
  padding: 0.75rem 0.5rem;
  display: flex;
  justify-content: center;
  align-items: center;
  margin: 10px auto;
  font-weight: bold;
}

button img {
  padding-right: 5px;
}

.shuffle-card-move {
  transition: transform 0.8s ease-in;
}
</style>
