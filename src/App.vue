<template>
  <div class="outer p-3" :class="dark_mode ? 'dark' : 'light'" draggable="false">
    <h2 class="text-center" draggable="false" @click="reset" style="cursor: pointer">
      Photographic Memory
    </h2>
    <div v-if="!game_start" class="d-flex align-items-center justify-content-center" style="height: 70vh">
      <div class="m-2">
        <select v-model="selectedDifficulty" class="form-select" style="width: 10rem"
          aria-label="Default select example">
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
    <div class="d-flex align-items-center justify-content-center" draggable="false">
      <div class="grid-container" :style="{
        gridTemplateColumns: 'repeat(' + selectedDifficulty.value + ', 1fr)',
        gridTemplateRows: 'repeat(' + selectedDifficulty.value + ', 1fr)',
      }" draggable="false" v-show="game_start">
        <div class="grid-item" v-for="(card, i) in cards" :key="i" draggable="false">
          <div class="flip-card" draggable="false">
            <div class="flip-card-inner" :class="{ flipped: card.show }" draggable="false">
              <div class="flip-card-front" draggable="false">
                <div class="back-icon" @click="flip($event, card)" draggable="false"></div>
              </div>
              <div class="flip-card-back" draggable="false">
                <div style="width: 100%; height: 100%; position: relative" draggable="false">
                  <img loading="eager" :src="getCharIcon(card.num)" style="
                      width: 100%;
                      height: 100%;
                      object-fit: cover;
                      position: absolute;
                      top: 0;
                      left: 0;
                    " draggable="false" />
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
    <!-- Dark mode -->
    <div class="dark-mode">
      <i class="material-icons md-36 text-white" v-if="dark_mode" @click="toggleDarkmode">light_mode</i>
      <i class="material-icons md-36 text-dark" v-else @click="toggleDarkmode">dark_mode</i>
    </div>
    <div class="footer">
      <!-- Signature -->
      <strong>
        made by
        <a href="https://github.com/JasonEst-11/Photographic-Memory" target="_blank">JasonEst-11</a>
      </strong>
      <!-- Jazz music -->
      <div class="d-flex justify-content-between align-items-center">
        <div class="me-2">
          Do you like Jazz? I do.🎹🎺
        </div>
        <div>
          <i class="material-icons md-36 " :class="dark_mode ? 'text-white' : 'text-dark'" v-if="!jazz"
            @click="playJazz">play_arrow</i>
          <i class="material-icons md-36 " :class="dark_mode ? 'text-white' : 'text-dark'" v-else @click="pauseJazz">pause</i>
        </div>
        <div>
          <audio ref="audio">
            <source src="/src/assets/bg-jazz.mp3" type="audio/mpeg">
          </audio>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      difficultyOptions: [
        {
          label: "Easy",
          value: 2,
          time: 1,
        },
        {
          label: "Medium",
          value: 4,
          time: 8,
        },
        {
          label: "Hard",
          value: 6,
          time: 12,
        },
      ],
      selectedDifficulty: {
        label: "Easy",
        value: 2,
        time: 1,
      },
      cards: [],
      selected: [],
      tries: 0,
      char_map: [],
      game_start: false,
      dark_mode: false,
      jazz: false
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
          }, 300);
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
              title: "Perfect!!! 👏🎉",
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
              title: "Well done!",
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
    reset() {
      window.location.reload();
      this.$refs.audio.volume = 0.2
    },
    toggleDarkmode() {
      this.dark_mode = !this.dark_mode
      localStorage.setItem("dark", this.dark_mode)
    },
    playJazz() {
      this.jazz = true;
      this.$refs.audio.play();
      this.$refs.audio.volume = 0.2
    },
    pauseJazz() {
      this.jazz = false;
      this.$refs.audio.pause();
    }
  },
  mounted() {
    this.initData();
    this.shuffle();
    let darkmode = localStorage.getItem("dark")
    if (darkmode) {
      if (darkmode === "false") {
        this.dark_mode = false
      } else {
        this.dark_mode = true;
      }
    } else {
      localStorage.setItem("dark", this.dark_mode)
    }
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
