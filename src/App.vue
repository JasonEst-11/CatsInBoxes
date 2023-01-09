<template>
  <div class="outer">
    <div>
      <h2 class="text-center">Cats in boxes</h2>
    </div>
    <div class="game-wrapper d-flex align-items-center justify-content-center">
      <div class="container text-center row gx-1">
        <div class="col-3 " v-for="card in cards">
          <vueflip width="5rem" height="5rem" class="m-1" v-model="card.show" >
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
        { num: 1, show: true },
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
          name: "Jason",
          // icon: "https://cdn.discordapp.com/avatars/402180657276518430/a8cb30fef01f312543cd788850a28685.webp?size=32"
          icon: "https://wompampsupport.azureedge.net/fetchimage?siteId=7575&v=2&jpgQuality=100&width=700&url=https%3A%2F%2Fi.kym-cdn.com%2Fphotos%2Fimages%2Fnewsfeed%2F002%2F205%2F309%2F1d3.jpg"
        },
        {
          id: 2,
          name: "Yann",
          // icon: "https://cdn.discordapp.com/avatars/402095276455624705/029162f4e4a7acbebe32200e02a22c2d.webp?size=128"
          icon: "https://i.pinimg.com/originals/ba/92/7f/ba927ff34cd961ce2c184d47e8ead9f6.jpg"
        },
        {
          id: 3,
          name: "Lemon",
          // icon: "https://cdn.discordapp.com/avatars/401857557125267466/7fab2b86d04ed985eac04f04f2ee76cd.webp?size=80"
          icon: "https://i.pinimg.com/originals/59/54/b4/5954b408c66525ad932faa693a647e3f.jpg"
        },
        {
          id: 4,
          name: "Xristos",
          // icon: "https://cdn.discordapp.com/avatars/470610590440882187/c2f4205ec6291704322a603d627361d7.webp?size=80"
          icon: "https://imgflip.com/s/meme/Cute-Cat.jpg"
        },
        {
          id: 5,
          name: "Spuros",
          // icon: "https://cdn.discordapp.com/avatars/427214726037110795/2ea834fcacf13b2c19af66e55c214a07.webp?size=80"
          icon: "https://a.pinatafarm.com/312x296/ae7f8ccd22/sad-thumbs-up-cat.jpg"
        },
        {
          id: 6,
          name: "Vaggelis",
          // icon: "https://cdn.discordapp.com/avatars/180961496275222528/f8b91e2c509a54c370af259eef452c97.webp?size=80"
          icon: "https://a.pinatafarm.com/620x463/80d36a3f6e/scared-cat.jpg"
        },
        {
          id: 7,
          name: "Nikos",
          // icon: "https://cdn.discordapp.com/avatars/280042196391034880/3116b1db9f64c84b5f340c8daf1e140f.webp?size=80"
          icon: "https://ih1.redbubble.net/image.3205821918.1712/pp,840x830-pad,1000x1000,f8f8f8.jpg"
        },
        {
          id: 8,
          name: "Vlasis",
          // icon: "https://cdn.discordapp.com/avatars/323276544271319042/33feef99e6e3dbaff3b2b307abc9f4f4.webp?size=80"
          icon: "https://i.ytimg.com/vi/-_c1dLgTjoo/hqdefault.jpg"
        },
      ]
    }
  },

  methods: {
    shuffle() {
      this.tries = 0;
      let currentIndex = this.cards.length, randomIndex;
      //https://stackoverflow.com/questions/2450954/how-to-randomize-shuffle-a-javascript-array
      // While there remain elements to shuffle.
      while (currentIndex != 0) {
        // Pick a remaining element.
        randomIndex = Math.floor(Math.random() * currentIndex);
        currentIndex--;
        // And swap it with the current element.
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
@import url('https://fonts.googleapis.com/css2?family=Rubik+Bubbles&display=swap');

.outer {
  /* background-color: rgb(47 49 54); */
  height: 100vh;
  font-family: 'Rubik Bubbles', cursive;
}

.icon {
  background-image: url('https://helloartsy.com/wp-content/uploads/kids/cats/how-to-draw-a-cat-in-a-box/how-to-draw-a-cat-in-a-box-step-6.jpg');
  background-repeat: no-repeat;
  background-attachment: fixed;
  background-position: center; 
  background-size: 80px ;
  height: 80px;
  width: 80px;
  border-radius: 10px;
  padding: 0%;
}



.game-wrapper {
  height: 80vh;
}



/* ---------------------------------------------- */
</style>
