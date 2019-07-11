<template>
  <div class="boss" :class="{ 'defeated':isDefeated, 'active':isActive }">
    <section class="waiting">
      <div v-for="(w, i) in waiting" :key="i"
        class="avatar"
        :data-id="w.toLowerCase()"
        @click="$emit('start', w)"
      ></div>
    </section>

    <div v-show="isActive" class="label">{{boss.id}}</div>
    <img src="../assets/crown.png" class="crown">
    <img v-if="level === 1" src="../assets/boss/1.jpg">
    <img v-if="level === 2" src="../assets/boss/2.png">
    <img v-if="level === 3" src="../assets/boss/3.png">
    <img v-if="level === 4" src="../assets/boss/4.png">
    <img v-if="level === 5" src="../assets/boss/5.png">
    <img v-if="level === 6" src="../assets/boss/6.png">
    <img v-if="level === 7" src="../assets/boss/7.png">
    <img v-if="level === 8" src="../assets/boss/8.png">
    <img v-if="level === 9" src="../assets/boss/9.png">
    <img v-if="level === 10" src="../assets/boss/10.png">
    <img v-if="level === 11" src="../assets/boss/11.png">
    <img v-if="level === 12" src="../assets/boss/12.png">

    <section class="kills">
      <div v-for="(l, i) in killed" :key="i"
        class="loser avatar"
        :data-survive="survivors.includes(l.id)"
        :data-id="l.id.toLowerCase()"
        @click="$emit('survive', l.id)"
        @dblclick="$emit('resurrect', l.id)"
      ></div>
    </section>
  </div>
</template>

<script>
export default {
  name: 'Boss',
  props: ['boss', 'currentBoss', 'level', 'dead', 'fighting', 'player', 'survivors'],
  data() {
    return {
      active: false,
      life: 3,
    };
  },
  computed: {
    isDefeated() {
      return (this.currentBoss + 1 > this.level);
    },
    isActive() {
      return (
        (this.currentBoss !== null && this.currentBoss + 1 === this.level)
        || !this.player
      );
    },
    killed() {
      return this.dead.filter(d => d.boss + 1 === this.level);
    },
    waiting() {
      const list = [];

      // eslint-disable-next-line
      for (let p in this.fighting) {
        if (
          p !== this.player
          && this.fighting[p] === this.level - 1
        ) list.push(p);
      }

      return list;
    },
  },
  methods: {
    updateLife() {
      this.life = (this.life === 0) ? 3 : this.life - 1;
    },
  },
};
</script>

<style scoped lang="scss">
.boss {
  flex-shrink: 0;
  position: relative;
  transition: filter 0.3s;
  width: calc(100% / 12);
  z-index: 1;

  &:last-of-type img:not(.crown) { border-color: #ffc93a; }

  &:not(:last-of-type) {
    &:after {
      background: white;
      content: '';
      height: 5px;
      position: absolute;
      top: 50%; left: 50%;
      width: 100%;
      z-index: 0;
    }

    .crown { display: none; }
  }

  img:not(.crown) {
    border: 5px solid white;
    border-radius: 100%;
    display: block;
    height: 100px;
    margin: 0 auto;
    max-width: 100px;
    position: relative;
    transition: transform 0.3s, filter 0.3s;
    width: 100%;
    z-index: 1;
  }

  .crown {
    position: absolute;
    bottom: 82%; left: 50%;
    transform: rotate(10deg);
    width: 32px;
    z-index: 2;
  }

  &:not(.active) {
    img {
      border-color: #999;
      filter: brightness(80%) grayscale(100%);
    }

    &:not(:last-of-type):after { background: #999; }
  }

  &.defeated {
    img {
      border-color: black;
      filter: brightness(50%) grayscale(75%);
      transform: rotate(180deg);
    }

    &:not(:last-of-type):after { background: black; }
  }

  .label {
    border-radius: 4px;
    color: white;
    font-weight: bold;
    padding: 5px 0;
    position: absolute;
    top: -35px; left: 0;
    text-align: center;
    width: 100%;
  }

  .waiting {
    position: absolute;
    bottom: 150%; left: 0;
    text-align: center;
    width: 100%;

    .avatar:hover { transform: scale(1.2); }
  }

  .avatar {
    border: 2px solid white;
    border-radius: 100%;
    cursor: pointer;
    display: inline-block;
    height: 48px;
    transition: filter 0.3s, transform 0.3s;
    width: 48px;
  }

  .kills {
    position: absolute;
    top: 120%; left: 0;
    text-align: center;
    width: 100%;

    .loser {
      border: 3px solid black;
      filter: brightness(90%) grayscale(70%);
      transform: rotate(180deg);

      &[data-survive] {
        border: 3px solid darkred;
        filter: grayscale(30%);
        transform: none;
      }
    }
  }
}
</style>
