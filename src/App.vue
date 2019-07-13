<template>
  <div id="app">
    <span class="trash" @dblclick="nuke"></span>

    <section class="bosses">
      <!-- <h2>The Bosses</h2> -->
      <Boss v-for="(b, i) in bosses"
        :key="i"
        :boss="b"
        :currentBoss="(player) ? boss : null"
        :level="i + 1"
        :dead=dead
        :fighting=fighting
        :player=player
        :survivors=survivors
        @start="newPlayer"
        @survive="toggleSurvival"
        @resurrect="unkillPlayer"
      />

      <section class="challengers">
        <Player v-for="(p, i) in players"
          :key="i"
          :player=p
          :fighting=fighting
          :currentPlayer="player"
          :dead=dead
          @updateBoss="updateBoss"
          @start="newPlayer"
          @kill="killPlayer"
        />
      </section>
    </section>

    <section class="underworld">
      UNDERWORLD
    </section>
  </div>
</template>

<script>
import store from 'store';
import Boss from './components/Boss.vue';
import Player from './components/Player.vue';

export default {
  name: 'app',
  components: {
    Boss,
    Player,
  },
  mounted() {
    if (store.get('s3-fight')) this.fighting = store.get('s3-fight');
    if (store.get('s3-dead')) this.dead = store.get('s3-dead');

    this.req = new XMLHttpRequest();

    this.req.onreadystatechange = () => {
      if (this.req.readyState === XMLHttpRequest.DONE) {
        const json = JSON.parse(this.req.responseText);
        console.log(this.req.responseText); // eslint-disable-line

        if (!this.done) {
          this.dead = json.dead || [];
          this.fighting = json.fighting || {};
          this.done = true;
        }
      }
    };

    this.req.open('GET', `https://api.jsonbin.io/b/${process.env.VUE_APP_BIN}/latest`, true);
    this.req.setRequestHeader('secret-key', `${process.env.VUE_APP_KEY}`);
    this.req.send();
  },
  data() {
    return {
      req: null,
      player: null,
      bosses: [
        { id: 'Pingu' },
        { id: 'Parzival' },
        { id: 'Anzu' },
        { id: 'Pedro' },
        { id: 'Gabeny' },
        { id: 'Tosh' },
        { id: 'Jet' },
        { id: 'Cold' },
        { id: 'Weasel' },
        { id: 'Srownel' },
        { id: 'Chip' },
        { id: 'Mishi' },
      ],
      players: [
        'ThisGuy',
        'Jakobi',
        'Joep',
        'Dino',
        'Platek',
        'Kerpa',
        'Child',
        'QQ',
        'Dzon',
        'Shadow31',
        'Kolaru',
        'Arilou',
        'Ziggy',
        'James',
        'Gustav',
        'SkeletN',
        'Duchu',
        'LeSnek',
        'Moose',
        'Eel',
        'Legoman',
        'Haru',
        'Pile',
        'Split',
        'Spider',
        'Bloocoon',
      ],
      dead: [],
      fighting: {},
      survivors: [],
      done: false,
    };
  },
  computed: {
    boss() {
      return this.fighting[this.player];
    },
  },
  methods: {
    updateBoss(n) {
      if (this.boss + n >= 0 && this.boss + n < 12) {
        this.fighting[this.player] += n;
        store.set('s3-fight', this.fighting);
        this.setJSON();
      }
    },
    newPlayer(p) {
      this.player = p;
      if (this.fighting[p] === undefined) this.$set(this.fighting, p, 0);
      store.set('s3-fight', this.fighting);
      this.setJSON();
    },
    killPlayer() {
      // Save death data
      this.dead.push({
        id: this.player,
        boss: this.boss,
      });

      for (let p in this.fighting) { // eslint-disable-line
        if (p === this.player) {
          this.$delete(this.fighting, p);
          this.player = null;
        }
      }

      store.set('s3-dead', this.dead);
      store.set('s3-fight', this.fighting);
      this.setJSON();
    },
    toggleSurvival(id) {
      if (this.survivors.includes(id)) {
        this.survivors = this.survivors.filter(s => s !== id);
      } else {
        this.survivors.push(id);
      }
    },
    unkillPlayer(p) {
      this.dead = this.dead.filter(d => d.id !== p);
      this.$set(this.fighting, p, 10);
      this.$root.$emit('survive', p);

      store.set('s3-dead', this.dead);
      store.set('s3-fight', this.fighting);
      this.setJSON();
    },
    nuke() {
      store.remove('s3-dead');
      store.remove('s3-fight');
      this.dead = [];
      this.fighting = {};
      this.survivors = [];
      this.setJSON();
    },
    setJSON() {
      this.req.open('PUT', `https://api.jsonbin.io/b/${process.env.VUE_APP_BIN}`, true);
      this.req.setRequestHeader('Content-type', 'application/json');
      this.req.setRequestHeader('secret-key', process.env.VUE_APP_KEY);
      this.req.setRequestHeader('versioning', false);
      this.req.send(JSON.stringify({
        dead: this.dead,
        fighting: this.fighting,
      }));
    },
  },
};
</script>

<style lang="scss">
* { box-sizing: border-box; }

html, body {
  margin: 0;
  padding: 0;
}

#app {
  background: #334;
  display: flex;
  flex-direction: column;
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  height: 100vh;
  overflow: hidden;
  width: 100vw;
}

.trash {
  background: url("./assets/trash.png") no-repeat;
  background-size: 100% auto;
  cursor: pointer;
  height: 32px;
  opacity: 0.5;
  position: fixed;
  top: 5px;
  right: 5px;
  width: 32px;
  z-index: 5;

  &:hover {
    opacity: 1;

    &:before {
      background: red;
      color: white;
      content: 'DANGER';
      padding: 8px;
      position: absolute;
      top: 0; right: 100%;
    }
  }
}

h2 {
  color: white;
  position: absolute;
  top: 0; left: 20px;
}

.bosses {
  align-items: center;
  background: linear-gradient(to top, darken(#334, 10%) 45%, transparent 50%);
  display: flex;
  flex-grow: 1;
  justify-content: space-between;
  padding: 0 50px;
  position: relative;
}

.challengers {
  background: linear-gradient(to top, darken(#334, 25%), transparent);
  padding: 0 50px;
  position: absolute;
  bottom: -30px; left: 0;
  text-align: center;
  width: 100%;
  z-index: 2;
}

.underworld {
  align-items: center;
  color: #334;
  display: flex;
  font-size: 5em;
  font-weight: bold;
  height: 50vh;
  justify-content: center;
  position: fixed;
  bottom: 0; left: 0;
  width: 100vw;
}

.avatar {
  background-size: 100% 100% !important;

  &[data-id="arilou"] { background: url('./assets/player/Arilou.png') no-repeat; }
  &[data-id="child"] { background: url('./assets/player/child.png') no-repeat; }
  &[data-id="bloocoon"] { background: url('./assets/player/bloocoon.png') no-repeat; }
  &[data-id="dino"] { background: url('./assets/player/dino.png') no-repeat; }
  &[data-id="duchu"] { background: url('./assets/player/Duchu.png') no-repeat; }
  &[data-id="dzon"] { background: url('./assets/player/Dzon.png') no-repeat; }
  &[data-id="eel"] { background: url('./assets/player/eel.png') no-repeat; }
  &[data-id="gustav"] { background: url('./assets/player/Gustav.png') no-repeat; }
  &[data-id="haru"] { background: url('./assets/player/Haru.png') no-repeat; }
  &[data-id="jakobi"] { background: url('./assets/player/jakobi.png') no-repeat; }
  &[data-id="james"] { background: url('./assets/player/James.png') no-repeat; }
  &[data-id="joep"] { background: url('./assets/player/Joep.png') no-repeat; }
  &[data-id="kerpa"] { background: url('./assets/player/Kerpa.png') no-repeat; }
  &[data-id="kolaru"] { background: url('./assets/player/Kolaru.png') no-repeat; }
  &[data-id="legoman"] { background: url('./assets/player/Legoman.png') no-repeat; }
  &[data-id="lesnek"] { background: url('./assets/player/lesnek.png') no-repeat; }
  &[data-id="moose"] { background: url('./assets/player/Moose.png') no-repeat; }
  &[data-id="pile"] { background: url('./assets/player/Pile.png') no-repeat; }
  &[data-id="platek"] { background: url('./assets/player/platek.png') no-repeat; }
  &[data-id="qq"] { background: url('./assets/player/QQ.png') no-repeat; }
  &[data-id="shadow31"] { background: url('./assets/player/Shadow31.png') no-repeat; }
  &[data-id="skeletn"] { background: url('./assets/player/SkeletN.png') no-repeat; }
  &[data-id="spider"] { background: url('./assets/player/Spider.png') no-repeat; }
  &[data-id="split"] { background: url('./assets/player/Split.png') no-repeat; }
  &[data-id="thisguy"] { background: url('./assets/player/ThisGuy.png') no-repeat; }
  &[data-id="ziggy"] { background: url('./assets/player/Ziggy.png') no-repeat; }
}
</style>
