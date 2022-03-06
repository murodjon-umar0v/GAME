<template>
  <div class="card" :class="flippedStyles" @click="selectCard">
    <div class="card-face is-front">
      <img style="width: 100%;" :src="`/images/${value}.png`" :alt="value">
      <img v-if="matched" src="../../public/images/checkmark.svg" class="icon-checkmark" />
    </div>
    <div class="card-face is-back">    </div>
  </div>
</template>

<script>
import { computed } from 'vue'

export default {
    props: {
      matched: {
        type: Boolean,
        default: false,
      },
      position: {
        type: Number,
        require: true,
      },
      value: {
        type: String,
        required: true,
      },
      visible: {
        type: Boolean,
            default: false,
      }
    },
    setup(props, context) {
      const flippedStyles = computed(() => {
        if (props.visible) {
          return 'is-flipped'
        } else {
          return 's'
        }
      }) 

      const selectCard = () => {
        context.emit('select-card', {
          position: props.position,
          faceValue: props.value
        })
      }


      return {
        flippedStyles,
        selectCard
      }
    }
}
</script>

<style scoped>
  .card {
    position: relative;
    transition: 0.5s transform ease-in;
    transform-style: preserve-3d;
  }

  .card-face {
    border-radius: 10px;
    width: 100%;
    height: 100%;
    position: absolute;
    display: flex;
    justify-content: center;
    align-items: center;
    backface-visibility: hidden;
  }

  .card-face.is-front {
    background-image: url('/images/card-bg.png');
    color: #fff;
    transform: rotateY(180deg);
  }

  .card-face.is-back {
    background-image: url('/images/card-bg-empty.png');
    color: #fff;
  }

  .icon-checkmark {
    position: absolute;
    right: 5px;
    bottom: 5px;
  }

  .card.is-flipped {
    transform: rotateY(180deg);
  }
</style>