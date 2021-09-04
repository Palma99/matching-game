<template>
  <div>
    <div :class="{ blurred: remainingMatch === 0 }">
      <div class="container">
        <transition-group class="board" :class="boardType.class" tag="section" name="card-shuffle">
          <Card
            v-for="card in cardList"
            @flip-card="flipCard"
            :key="`${card.value}-${card.number}`"
            :value="card.value"
            :id="card.id"
            :visible="card.visible"
            :enabled="!missMatchingCardsFlipped && !shuffuling"
            :match="card.match"
            :imageSrc="card.imageUrl"
            :sizeClass="card.sizeClass"
          />
        </transition-group>
      </div>
      <button class="start-btn" @click="shuffleCards(3, true)">Start</button>
      <h2>{{ remainingMatch }} {{ textCoppie }}</h2>
    </div>
    <modal :visible="remainingMatch === 0" />
  </div>
</template>

<script>
import { computed, ref, watch } from 'vue';
import Card from '../components/Card.vue';
import Modal from '../components/Modal.vue';

const modeMap = new Map()
  .set('facile', { class: 'easy', size: 16 })
  .set('medio', { class: 'medium', size: 36 })
  .set('difficile', { class: 'difficult', size: 64 });

const images = [
  'https://static-cdn.jtvnw.net/emoticons/v2/304233739/default/light/3.0',
  'https://static-cdn.jtvnw.net/emoticons/v2/304233099/default/light/3.0',
  'https://static-cdn.jtvnw.net/emoticons/v2/304233084/default/light/3.0',
  'https://static-cdn.jtvnw.net/emoticons/v2/303859215/default/light/3.0',
  'https://static-cdn.jtvnw.net/emoticons/v2/303859207/default/light/3.0',
  'https://static-cdn.jtvnw.net/emoticons/v2/303859192/default/light/3.0',
  'https://static-cdn.jtvnw.net/emoticons/v2/303859185/default/light/3.0',
  'https://static-cdn.jtvnw.net/emoticons/v2/306116312/default/light/3.0',
  'https://static-cdn.jtvnw.net/emoticons/v2/306132223/default/light/3.0',
  'https://static-cdn.jtvnw.net/emoticons/v2/306132235/default/light/3.0',
  'https://static-cdn.jtvnw.net/emoticons/v2/306132240/default/light/3.0',
  'https://static-cdn.jtvnw.net/emoticons/v2/306132248/default/light/3.0',
  'https://static-cdn.jtvnw.net/emoticons/v2/306132352/default/light/3.0',
  'https://cdn.betterttv.net/emote/5fd50ef8f0c558349268c8b7/3x',
  'https://cdn.betterttv.net/emote/5f3fc5813212445d6fb4a953/3x',
  'https://cdn.betterttv.net/emote/6068a524a407570b72f2a646/3x',
  'https://cdn.betterttv.net/emote/6078bf4e39b5010444d0007b/3x',
  'https://cdn.betterttv.net/emote/607d4b1b39b5010444d0222e/3x',
  'https://cdn.betterttv.net/emote/6087ef8539b5010444d0719c/3x',
  'https://cdn.betterttv.net/emote/6074f66d39b5010444cfe2aa/3x',
  'https://cdn.betterttv.net/emote/60982e8a39b5010444d0e814/3x',
  'https://cdn.betterttv.net/emote/5f9865eb6f583802e389dc40/3x',
  'https://cdn.betterttv.net/emote/6043db7f306b602acc5981be/3x',
  'https://cdn.betterttv.net/emote/604376c1306b602acc597f17/3x',
  'https://cdn.betterttv.net/emote/5fcf8f06cac2fb4621e5592d/3x',
  'https://cdn.betterttv.net/emote/5f36f9ebf93bbe5d717ee20a/3x',
  'https://cdn.frankerfacez.com/emote/439694/2',
  'https://cdn.betterttv.net/emote/5b1740221c5a6065a7bad4b5/3x',
  'https://cdn.betterttv.net/emote/5f611edf0b77be6bfcaddf39/3x',
  'https://cdn.betterttv.net/emote/605b91727493072efdeb3d00/3x',
  'https://cdn.betterttv.net/emote/5fdf77da22274a347e6bccb4/3x',
  'https://cdn.betterttv.net/emote/5f985d85710f8302f0c947e6/3x',
].sort(() => (Math.random() > 0.5 ? 1 : -1));

export default {
  name: 'Home',
  components: { Card, Modal },
  props: {
    mode: {
      type: String,
      required: true,
    },
  },
  setup(props) {
    const shuffuling = ref([]);
    const cardList = ref([]);
    const selected = ref([]);
    const missMatchingCardsFlipped = ref(false);

    const boardType = computed(() => modeMap.get(props.mode));

    const remainingMatch = computed(() => {
      const found = cardList.value.filter((card) => card.match).length;
      return (boardType.value.size - found) / 2;
    });

    const textCoppie = computed(() => {
      if (remainingMatch.value === 1) {
        return 'coppia rimanente';
      }
      return 'coppie rimanenti';
    });

    const fillCardList = () => {
      cardList.value = [];
      Array.from({ length: boardType.value.size / 2 }).forEach((_, i) => {
        cardList.value.push({
          value: i,
          imageUrl: images[i],
          id: i * 2,
          number: 1,
          visible: true,
          sizeClass: props.mode,
        });
        cardList.value.push({
          value: i,
          imageUrl: images[i],
          number: 2,
          id: i * 2 + 1,
          visible: true,
          sizeClass: props.mode,
        });
      });
    };

    watch(boardType, fillCardList);

    fillCardList();

    const flipAllCards = () => new Promise((res) => {
      cardList.value = cardList.value.map((card) => ({
        ...card,
        visible: false,
        match: false,
      }));
      setTimeout(res, 600);
    });

    const shuffleCards = async (n, flipAll = false) => {
      shuffuling.value = n !== 0;
      if (n === 0) {
        cardList.value = cardList.value.map((card, i) => ({
          ...card,
          id: i,
          match: false,
          visible: false,
        }));
        return;
      }
      if (flipAll) {
        await flipAllCards();
      }
      cardList.value = cardList.value.sort(() => (Math.random() > 0.5 ? 1 : -1));

      setTimeout(() => shuffleCards(n - 1), 650);
    };

    const flipCard = (id) => {
      if (selected.value.length === 1) {
        if (selected.value[0].id === id) return;
      }
      if (selected.value.length === 2) {
        selected.value.length = 0;
      }
      selected.value.push(cardList.value[id]);
      cardList.value[id].visible = true;
    };

    watch(selected.value, (selectedCards) => {
      if (selectedCards.length === 2) {
        if (selectedCards[0].value === selectedCards[1].value) {
          cardList.value[selectedCards[0].id].match = true;
          cardList.value[selectedCards[1].id].match = true;
        } else {
          missMatchingCardsFlipped.value = true;
          setTimeout(() => {
            cardList.value[selectedCards[0].id].visible = false;
            cardList.value[selectedCards[1].id].visible = false;
            selected.value.length = 0;
            missMatchingCardsFlipped.value = false;
          }, 1000);
        }
      }
    });

    return {
      shuffuling,
      textCoppie,
      remainingMatch,
      shuffleCards,
      boardType,
      cardList,
      flipCard,
      selected,
      missMatchingCardsFlipped,
    };
  },
};
</script>

<style lang="scss" scoped>
h2 {
  font-size: 2rem;
}

.start-btn {
  border-radius: 0.5rem;
  border: none;
  background-color: #2b303a;
  color: #fff;
  font-size: 2rem;
  width: fit-content;
  padding: 0.5rem 1.5rem;
  cursor: pointer;
  margin-top: 1rem;

  &:hover {
    background-color: #000000;
  }
}

.container {
  width: 100vw;
  display: flex;
  justify-content: center;
  margin-top: 1rem;
  overflow: hidden;
}

.blurred {
  filter: blur(5px);
}

.board {
  display: grid;
  grid-column-gap: 0.2rem;
  grid-row-gap: 0.2rem;
  width: fit-content;
  height: fit-content;
}

.board.easy {
  grid-template-columns: repeat(4, 1fr);
  grid-template-rows: repeat(4, 1fr);
}

.board.medium {
  grid-template-columns: repeat(6, 1fr);
  grid-template-rows: repeat(6, 1fr);
}

.board.difficult {
  grid-template-columns: repeat(8, 1fr);
  grid-template-rows: repeat(8, 1fr);
}

.card-shuffle-move {
  transition: transform 0.6s ease-in-out;
}
</style>
