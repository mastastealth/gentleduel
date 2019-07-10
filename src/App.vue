<template>
  <div id="app">
    <section class="bosses">
      <h2>The Bosses</h2>

      <Boss v-for="(b, i) in bosses"
        :key="i"
        :boss="b"
        :currentBoss="(player) ? boss : null"
        :level="i + 1"
        :dead=dead
        :fighting=fighting
        :player=player
        @start="newPlayer"
      />

      <section class="challengers">
        <Player v-for="(p, i) in players"
          :key="i"
          :player=p
          :fighting=fighting
          :currentPlayer="player"
          @updateBoss="updateBoss"
          @start="newPlayer"
          @kill="killPlayer"
        />
      </section>
    </section>
    <section class="underworld">
      <h2>The Underworld</h2>
    </section>
  </div>
</template>

<script>
import Boss from './components/Boss.vue';
import Player from './components/Player.vue';

export default {
  name: 'app',
  components: {
    Boss,
    Player,
  },
  data() {
    return {
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
      ],
      dead: [],
      fighting: {},
    };
  },
  computed: {
    boss() {
      return this.fighting[this.player];
    },
  },
  methods: {
    updateBoss(n) {
      if (this.boss + n >= 0 && this.boss + n < 12) this.fighting[this.player] += n;
    },
    newPlayer(p) {
      this.player = p;
      if (this.fighting[p] === undefined) this.$set(this.fighting, p, 0);
    },
    killPlayer() {
      // Save death data
      this.dead.push({
        id: this.player.toLowerCase(),
        boss: this.boss,
      });

      for (let p in this.fighting) { // eslint-disable-line
        if (p === this.player) {
          this.$delete(this.fighting, p);
        }
      }
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

h2 {
  color: white;
  position: absolute;
  top: 0; left: 20px;
}

.bosses {
  align-items: center;
  display: flex;
  flex-grow: 1;
  justify-content: space-between;
  padding: 0 50px;
  position: relative;
}

.challengers {
  padding: 0 50px;
  position: absolute;
  bottom: -30px; left: 0;
  text-align: center;
  width: 100%;
  z-index: 2;
}

.underworld {
  background: darken(#334, 10%);
  height: 20vh;
  position: relative;
  z-index: 3;
}

.avatar {
  background-size: 100% 100% !important;

  &[data-id="arilou"] { background: url('./assets/player/Arilou.png') no-repeat; }
  &[data-id="child"] { background: url('./assets/player/child.png') no-repeat; }
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
  &[data-id="split"] { background: url('./assets/player/Split.png') no-repeat; }
  &[data-id="thisguy"] { background: url('./assets/player/ThisGuy.png') no-repeat; }
  &[data-id="ziggy"] { background: url('./assets/player/Ziggy.png') no-repeat; }
}
</style>
