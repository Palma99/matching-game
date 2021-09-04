<template>
  <div
    @click="flipCard"
    class="card"
    :class="{
      'is-matching': match,
      easy: sizeClass === 'facile',
      medium: sizeClass === 'medio',
      difficult: sizeClass === 'difficile',
    }"
  >
    <div class="card-inner" :class="{ 'is-flipped': visible }">
      <div class="card-face card-face-front"></div>
      <div class="card-face card-face-back">
        <img :src="imageSrc" />
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    imageSrc: {
      type: String,
      required: true,
    },
    value: {
      type: Number,
      required: true,
    },
    id: {
      type: Number,
      required: true,
    },
    visible: {
      type: Boolean,
      required: true,
    },
    enabled: {
      type: Boolean,
      required: true,
    },
    match: {
      type: Boolean,
    },
    sizeClass: {
      type: String,
      required: true,
    },
  },
  name: 'Card',
  emits: ['flip-card'],
  setup(props, { emit }) {
    const flipCard = () => {
      if (props.visible || !props.enabled) return;
      emit('flip-card', props.id);
    };
    return {
      flipCard,
    };
  },
};
</script>

<style scoped>
.card {
  /* width: 150px;
  height: 150px; */
  background-color: transparent;
  perspective: 1000px;
}

.easy {
  width: 18vh;
  height: 18vh;
}

.medium {
  width: 12vh;
  height: 12vh;
}

.difficult {
  width: 10vh;
  height: 10vh;
}

.is-matching {
  animation: match 1s;
}

@keyframes match {
  50% {
    transform: scale(1.15);
    z-index: 999;
  }
  100% {
    transform: scale(1);
    z-index: 999;
  }
}

.card-inner {
  position: relative;
  width: 100%;
  height: 100%;
  cursor: pointer;
  text-align: center;
  transition: transform 0.8s;
  transform-style: preserve-3d;
}

.card-inner.is-flipped {
  transform: rotateY(180deg);
}

.card-face {
  position: absolute;
  width: 100%;
  height: 100%;
  color: #fff;
  text-align: center;
  font-weight: bold;
  border: 1px solid #fff;
  font-size: 2rem;
  -webkit-backface-visibility: hidden;
  backface-visibility: hidden;
  border-radius: 0.7rem;
  display: flex;
  justify-content: center;
  align-items: center;
}

img {
  border-radius: 0.5rem;
  width: 60%;
  height: 60%;
  box-shadow: 0px 0px 17px #0000003f;
}

.is-matching > * > .card-face {
  background-color: #e55d3c;
}

.card-face-front {
  background: #264653;
}

.card-face-back {
  background: #86a370;
  transform: rotateY(180deg);
}
</style>
