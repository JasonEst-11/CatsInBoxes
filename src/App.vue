<template>
  <div class="outer" draggable="false">
    <div class="mt-3" draggable="false">
      <h2 class="text-center" draggable="false">Photographic Memory</h2>
    </div>
    <div
      class="d-flex align-items-center justify-content-center"
      v-if="!processing"
      draggable="false"
    >
      <div class="grid-container" draggable="false">
        <div
          class="grid-item"
          v-for="(card, i) in cards"
          :key="i"
          draggable="false"
        >
          <div class="flip-card" draggable="false">
            <div
              class="flip-card-inner"
              :class="{ flipped: card.show }"
              draggable="false"
            >
              <div class="flip-card-front" draggable="false">
                <div
                  class="back-icon"
                  @click="flip($event, card)"
                  draggable="false"
                ></div>
              </div>
              <div class="flip-card-back" draggable="false">
                <div
                  style="width: 100%; height: 100%; position: relative"
                  draggable="false"
                >
                  <img
                    :src="getCharIcon(card.num)"
                    style="
                      width: 100%;
                      height: 100%;
                      object-fit: cover;
                      position: absolute;
                      top: 0;
                      left: 0;
                    "
                    draggable="false"
                  />
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
    <div class="d-flex justify-content-center">
      <div>
        <h5>Attempts: {{ tries }}</h5>
      </div>
    </div>
    <div class="d-flex justify-content-center">
      <div>
        <button type="button" class="btn btn-primary" @click="reset">
          Reset
        </button>
      </div>
    </div>
    <strong class="ms-5">
      made by
      <a
        href="https://github.com/JasonEst-11/Photographic-Memory"
        target="_blank"
        >JasonEst-11</a
      >
    </strong>
  </div>
</template>

<script>
export default {
  data() {
    return {
      cards: [],
      selected: [],
      tries: 0,
      char_map: [],
    };
  },

  methods: {
    shuffle() {
      this.tries = 0;
      this.selected = [];
      let currentIndex = this.cards.length,
        randomIndex;
      while (currentIndex != 0) {
        randomIndex = Math.floor(Math.random() * currentIndex);
        currentIndex--;
        [this.cards[currentIndex], this.cards[randomIndex]] = [
          this.cards[randomIndex],
          this.cards[currentIndex],
        ];
      }
      this.cards.forEach((el) => (el.show = false));
    },
    getCharIcon(id) {
      return this.char_map.find((el) => el.id === id).icon;
    },
    flip(event, card) {
      if (this.selected.length != 2) {
        card.show = !card.show;
        this.selected.push(card.num);
        if (this.selected.length === 2) {
          this.tries++;
          setTimeout(() => {
            if (this.selected[0] === this.selected[1]) {
              this.selected = [];
              this.checkIfWin();
            } else {
              this.cards.map((el) => {
                if (
                  el.num === this.selected[0] ||
                  el.num === this.selected[1]
                ) {
                  el.show = false;
                  return el;
                } else {
                  return el;
                }
              });
              this.selected = [];
            }
          }, 700);
        }
      }
    },
    initData() {
      this.cards = [];
      this.char_map = [];
      for (let i = 1; i <= 8; i++) {
        //Add to cards
        this.cards.push({ num: i, show: false });
        this.cards.push({ num: i, show: false });
        let url = "https://picsum.photos/200/200?random=" + i;
        //Add to char_map
        this.char_map.push({
          id: i,
          icon: url,
        });
      }
    },
    checkIfWin() {
      let res = this.cards.filter((el) => el.show === false);
      if (res.length === 0) {
        alert("Well done!");
        this.reset();
      }
    },
    reset() {
      window.location.reload();
    },
  },
  mounted() {
    this.initData();
    this.shuffle();
  },
};
</script>

<style scoped>
@import url("https://fonts.googleapis.com/css2?family=Roboto&display=swap");
body {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
  margin: 0;
  background-color: #f4f4f4;
}
.grid-container {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  grid-template-rows: repeat(4, 1fr);
  width: 80vmin;
  height: 80vmin;
  gap: 10px;
  -webkit-user-select: none; /* Safari */
  -ms-user-select: none; /* IE 10 and IE 11 */
  user-select: none; /* Standard syntax */
}

.grid-item {
  background-color: transparent;
  display: flex;
  justify-content: center;
  align-items: center;
  font-size: 1.5em;
  color: white;
  -webkit-user-select: none; /* Safari */
  -ms-user-select: none; /* IE 10 and IE 11 */
  user-select: none; /* Standard syntax */
}
.outer {
  /* background-color: rgb(240, 211, 132); */
  font-family: "Roboto", sans-serif;
  -webkit-user-select: none; /* Safari */
  -ms-user-select: none; /* IE 10 and IE 11 */
  user-select: none; /* Standard syntax */
}

.back-icon {
  background-image: url("https://lh3.googleusercontent.com/EbXw8rOdYxOGdXEFjgNP8lh-YAuUxwhOAe2jhrz3sgqvPeMac6a6tHvT35V6YMbyNvkZL4R_a2hcYBrtfUhLvhf-N2X3OB9cvH4uMw=w1064-v0");
  background-size: cover;
  width: 100%;
  height: 100%;
  -webkit-user-select: none; /* Safari */
  -ms-user-select: none; /* IE 10 and IE 11 */
  user-select: none; /* Standard syntax */
}

.game-wrapper {
  height: 60vh;
  -webkit-user-select: none; /* Safari */
  -ms-user-select: none; /* IE 10 and IE 11 */
  user-select: none; /* Standard syntax */
}
.flip-card {
  perspective: 1000px;
  width: 100%;
  height: 100%;
  cursor: pointer;
  -webkit-user-select: none; /* Safari */
  -ms-user-select: none; /* IE 10 and IE 11 */
  user-select: none; /* Standard syntax */
}

.flip-card-inner {
  width: 100%;
  height: 100%;
  transition: transform 0.3s;
  transform-style: preserve-3d;
  position: relative;
  -webkit-user-select: none; /* Safari */
  -ms-user-select: none; /* IE 10 and IE 11 */
  user-select: none; /* Standard syntax */
}

.flipped {
  transform: rotateY(180deg);
}

.flip-card-front,
.flip-card-back {
  width: 100%;
  height: 100%;
  backface-visibility: hidden;
  position: absolute;
  top: 0;
  left: 0;
}

.flip-card-front {
  background-color: #fff;
  display: flex;
  align-items: center;
  justify-content: center;
}

.flip-card-back {
  background-color: #f8f8f8;
  transform: rotateY(180deg);
  display: flex;
  align-items: center;
  justify-content: center;
}
</style>
