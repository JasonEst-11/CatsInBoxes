<template>
  <div class="outer">
    <div class="mt-5">
      <h2 class="text-center">Photographic Memory</h2>
    </div>
    <div class="game-wrapper d-flex align-items-center justify-content-center">
      <div class="container text-center row gx-1">
        <div class="col-3 " v-for="card, i in cards" :key="i">
          <vueflip width="5rem" height="5rem" class="m-1" v-model="card.show">
            <template v-slot:front>
              <div @click="flip($event, card)" class="icon">
              </div>
            </template>
            <template v-slot:back>
              <div class="icon">
                <img :src="getCharIcon(card.num)" width="80" height="80">
              </div>
            </template>
          </vueflip>
        </div>
      </div>
    </div>
    <div class="d-flex justify-content-center">
      <div>
        <h5>
          Attempts: {{ tries }}
        </h5>
      </div>
    </div>
    <div class="d-flex justify-content-center">
      <div>
        <input type="button" value="Reset" @click="shuffle" />
      </div>
    </div>
  </div>
</template>

<script>
import { VueFlip } from 'vue-flip'
export default {
  components: {
    vueflip: VueFlip
  },
  data() {
    return {
      cards: [
        { num: 1, show: false },
        { num: 1, show: false },
        { num: 2, show: false },
        { num: 2, show: false },
        { num: 3, show: false },
        { num: 3, show: false },
        { num: 4, show: false },
        { num: 4, show: false },
        { num: 5, show: false },
        { num: 5, show: false },
        { num: 6, show: false },
        { num: 6, show: false },
        { num: 7, show: false },
        { num: 7, show: false },
        { num: 8, show: false },
        { num: 8, show: false },
      ],
      selected: [],
      tries: 0,
      char_map: [
        {
          id: 1,
          icon: "https://picsum.photos/200/200?random=1"
        },
        {
          id: 2,
          icon: "https://picsum.photos/200/200?random=2"
        },
        {
          id: 3,
          icon: "https://picsum.photos/200/200?random=3"
        },
        {
          id: 4,
          icon: "https://picsum.photos/200/200?random=4"
        },
        {
          id: 5,
          icon: "https://picsum.photos/200/200?random=5"
        },
        {
          id: 6,
          icon: "https://picsum.photos/200/200?random=6"
        },
        {
          id: 7,
          icon: "https://picsum.photos/200/200?random=7"
        },
        {
          id: 8,
          icon: "https://picsum.photos/200/200?random=8"
        },
      ]
    }
  },

  methods: {
    shuffle() {
      this.tries = 0;
      this.selected = []
      let currentIndex = this.cards.length, randomIndex;
      while (currentIndex != 0) {       
        randomIndex = Math.floor(Math.random() * currentIndex);
        currentIndex--;      
        [this.cards[currentIndex], this.cards[randomIndex]] = [
          this.cards[randomIndex], this.cards[currentIndex]];
      }
      this.cards.forEach(el => el.show = false)
    },
    getCharIcon(id) {
      return this.char_map.find(el => el.id === id).icon
    },
    flip(event, card) {
      if (this.selected.length != 2) {
        card.show = !card.show
        this.selected.push(card.num)
        if (this.selected.length === 2) {
          this.tries++
          setTimeout(() => {
            if (this.selected[0] === this.selected[1]) {
              this.selected = []
              this.checkIfWin();
            } else {
              this.cards.map(el => {
                if (el.num === this.selected[0] || el.num === this.selected[1]) {
                  el.show = false;
                  return el
                } else {
                  return el
                }
              })
              this.selected = []
            }
          }, 1000);
        }
      }
    },

    checkIfWin() {
      let res = this.cards.filter(el => el.show === false)
      if (res.length === 0) {
        alert("Congrats final score: " + this.tries)
        this.shuffle()
      }
    }
  },
  mounted() {
    this.shuffle();
  }
}
</script>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Roboto&display=swap');

.outer {
  /* background-color: rgb(240, 211, 132); */
  font-family: 'Roboto', sans-serif;
}

.icon {
  background-image: url('https://lh3.googleusercontent.com/EbXw8rOdYxOGdXEFjgNP8lh-YAuUxwhOAe2jhrz3sgqvPeMac6a6tHvT35V6YMbyNvkZL4R_a2hcYBrtfUhLvhf-N2X3OB9cvH4uMw=w1064-v0');
  background-repeat: no-repeat;
  background-attachment: fixed;
  background-position: center;
  background-size: 5rem;
  min-width: 80px;
  min-height: 80px;
  border-radius: 10px;
  padding: 0%;
}

.game-wrapper {
  height: 60vh;
}

</style>
