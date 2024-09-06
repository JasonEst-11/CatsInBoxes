<template>
  <div class="outer" draggable="false">
    <div class="mt-3" draggable="false">
      <h2 class="text-center" draggable="false">Photographic Memory</h2>
    </div>
    <div
      v-if="!game_start"
      class="d-flex align-items-center justify-content-center"
      style="height: 70vh"
    >
      <div class="m-2">
        <select
          v-model="selectedDifficulty"
          class="form-select"
          style="width: 10vw"
          aria-label="Default select example"
        >
          <option v-for="diff in difficultyOptions" :key="diff" :value="diff">
            {{ diff.label }}
          </option>
        </select>
      </div>
      <div class="m-2">
        <button type="button" class="btn btn-primary" @click="start">
          Start
        </button>
      </div>
    </div>
    <div
      class="d-flex align-items-center justify-content-center"
      draggable="false"
    >
      <div
        class="grid-container"
        :style="{
          gridTemplateColumns: 'repeat(' + selectedDifficulty.value + ', 1fr)',
          gridTemplateRows: 'repeat(' + selectedDifficulty.value + ', 1fr)',
        }"
        draggable="false"
        v-show="game_start"
      >
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
                    loading="eager"
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
  </div>

  <strong class="signature">
    made by
    <a href="https://github.com/JasonEst-11/Photographic-Memory" target="_blank"
      >JasonEst-11</a
    >
  </strong>
</template>

<script>
export default {
  data() {
    return {
      difficultyOptions: [
        {
          label: "Easy",
          value: 2,
          time: 0.5,
        },
        {
          label: "Medium",
          value: 4,
          time: 3.5,
        },
        {
          label: "Hard",
          value: 6,
          time: 6,
        },
      ],
      selectedDifficulty: {
        label: "Easy",
        value: 2,
        time: 0.5,
      },
      cards: [],
      selected: [],
      tries: 0,
      char_map: [],
      game_start: false,
    };
  },
  computed: {
    difficultyPairs() {
      let grid = this.selectedDifficulty.value;
      return (grid * grid) / 2;
    },
  },
  methods: {
    initData() {
      this.cards = [];
      this.char_map = [];
      for (let i = 1; i <= this.difficultyPairs; i++) {
        //Add to cards
        this.cards.push({ num: i, show: false });
        this.cards.push({ num: i, show: false });
        setTimeout(() => {
          let url =
            "https://picsum.photos/200?random=" +
            new Date().getTime() +
            i +
            ".webp";
          //Add to char_map
          this.char_map.push({
            id: i,
            icon: url,
          });
        }, 1);
      }
    },
    shuffle() {
      this.tries = 0;
      this.selected = [];
      let currentIndex = this.cards.length;
      let randomIndex;
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
          }, 500);
        }
      }
    },
    getCharIcon(id) {
      let char = this.char_map.find((el) => el.id === id);
      if (char) {
        return char.icon;
      }
    },
    checkIfWin() {
      let res = this.cards.filter((el) => el.show === false);
      if (res.length === 0) {
        if (
          this.tries <= this.difficultyPairs &&
          this.selectedDifficulty.value === 2
        ) {
          this.$swal
            .fire({
              title: "Not even a challenge huh? 😎",
              showCancelButton: true,
              confirmButtonText: "Replay",
            })
            .then((result) => {
              if (result.isConfirmed) {
                this.start();
              } else {
                this.game_start = false;
              }
            });
        } else if (this.tries <= this.difficultyPairs) {
          this.$swal
            .fire({
              title: "Perfect!!! Did you cheat? 🧐",
              showCancelButton: true,
              confirmButtonText: "Replay",
            })
            .then((result) => {
              if (result.isConfirmed) {
                this.start();
              } else {
                this.game_start = false;
              }
            });
        } else {
          this.$swal
            .fire({
              title: "Well done",
              text: "Total attempts: " + this.tries,
              showCancelButton: true,
              confirmButtonText: "Replay",
            })
            .then((result) => {
              if (result.isConfirmed) {
                this.start();
              } else {
                this.game_start = false;
              }
            });
        }
      }
    },
    start() {
      this.game_start = true;
      this.initData();
      this.shuffle();
      this.cards.forEach((el) => (el.show = true));
      setTimeout(() => {
        this.cards.forEach((el) => (el.show = false));
      }, 1000 * this.selectedDifficulty.time);
    },
  },
  mounted() {
    this.initData();
    this.shuffle();
  },
  watch: {
    selectedDifficulty(newVal, oldVal) {
      if (newVal.value != oldVal.value) {
        this.initData();
        this.shuffle();
      }
    },
  },
};
</script>

<style scoped>
@import url("https://fonts.googleapis.com/css2?family=Roboto&display=swap");
* {
  -webkit-user-select: none; /* Safari */
  -ms-user-select: none; /* IE 10 and IE 11 */
  user-select: none; /* Standard syntax */
}
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
  /* grid-template-columns: repeat(4, 1fr);
  grid-template-rows: repeat(4, 1fr); */
  width: 80vmin;
  height: 80vmin;
  gap: 10px;
}

.grid-item {
  background-color: transparent;
  display: flex;
  justify-content: center;
  align-items: center;
  font-size: 1.5em;
  color: white;
}
.outer {
  /* background-color: rgb(240, 211, 132); */
  font-family: "Roboto", sans-serif;
}

.back-icon {
  background-image: url("https://lh3.googleusercontent.com/EbXw8rOdYxOGdXEFjgNP8lh-YAuUxwhOAe2jhrz3sgqvPeMac6a6tHvT35V6YMbyNvkZL4R_a2hcYBrtfUhLvhf-N2X3OB9cvH4uMw=w1064-v0");
  background-size: cover;
  width: 100%;
  height: 100%;
}

.game-wrapper {
  height: 60vh;
}
.flip-card {
  perspective: 1000px;
  width: 100%;
  height: 100%;
  cursor: pointer;
}

.flip-card-inner {
  width: 100%;
  height: 100%;
  transition: transform 0.3s;
  transform-style: preserve-3d;
  position: relative;
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

.signature {
  position: fixed;
  left: 3rem;
  bottom: 1rem;
}
</style>
