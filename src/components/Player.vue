<template>
  <div
    class="player"
    :class="{ 'active':isActive, 'dead':isDead, 'pause':isWaiting }"
    :style="style"
  >
    <div
      class="avatar"
      :data-id="player.toLowerCase()"
    ></div>

    <button v-if="!isActive" @click="startPlayer" class="start"></button>

    <template v-if="isActive">
      <footer class="hearts" @click="updateLife">
        <img v-if="life === 0" src="../assets/skull.png" class="heart">
        <img v-if="life >= 1" src="../assets/heart.png" class="heart">
        <img v-if="life >= 2" src="../assets/heart.png" class="heart">
        <img v-if="life >= 3" src="../assets/heart.png" class="heart">
      </footer>

      <button @click="$emit('updateBoss', -1)" class="prev"></button>
      <button @click="$emit('updateBoss', 1)" class="next"></button>
    </template>
  </div>
</template>

<script>
export default {
  name: 'Player',
  props: ['player', 'fighting', 'currentPlayer'],
  data() {
    return {
      life: 3,
    };
  },
  computed: {
    style() {
      return (this.isActive) ? `transform: translateX(calc((100vw - 100px)/12 * ${this.boss});` : '';
    },
    boss() {
      return this.fighting[this.player];
    },
    isDead() {
      return this.life === 0;
    },
    isActive() {
      return this.currentPlayer === this.player && this.life !== 0;
    },
    isWaiting() {
      return this.fighting[this.player] && this.currentPlayer !== this.player;
    },
  },
  methods: {
    updateLife() {
      this.life -= 1;
      if (this.life === 0) this.$emit('kill', this.player);
    },
    startPlayer() {
      this.active = true;
      this.$emit('start', this.player);
    },
  },
};
</script>

<style scoped lang="scss">
.player {
  display: inline-block;
  position: relative;
  text-align: center;
  transition: transform 0.4s;

  &:hover {
    .avatar { border-color: white; filter: none; }
  }

  &.active {
    position: absolute;
    top: -32vh; left: 50px;
    width: calc((100vw - 100px) / 12);

    &:before {
      background: url('../assets/swords.png') no-repeat;
      background-size: 100% 100%;
      content: '';
      height: 24px;
      position: absolute;
      top: -8px; left: 50%;
      transform: translateX(-50%);
      width: 24px;
      z-index: 1;
    }

    .avatar {
      border: 5px solid white;
      filter: drop-shadow(0 0 5px black);
    }
  }

  &.dead,
  &.pause {
    position: absolute;
    top: 100%;
  }

  .avatar {
    border: 5px solid #666;
    border-radius: 100%;
    display: inline-block;
    filter: brightness(80%) grayscale(50%);
    height: 60px;
    width: 60px;
  }

  &:not(:hover) {
    .hearts {
      opacity: 0;
      pointer-events: none;
      transform: translateY(-100%) scale(0.5);
    }

    button {
      opacity: 0;
      pointer-events: none;

      &.prev { transform: translateX(100%); }
      &.next { transform: translateX(-100%); }
    }
  }
}

.hearts {
  cursor: pointer;
  position: absolute;
  top: 100%; left: 0;
  transition: opacity 0.5s, transform 0.3s;
  width: 100%;

  .heart {
    width: 16px;
  }
}

button {
  background-size: 100% 100% !important;
  border: 0 none;
  cursor: pointer;
  height: 24px;
  position: absolute;
  top: 30%;
  transition: opacity 0.5s, transform 0.3s;
  width: 24px;

  &.start {
    background: url('../assets/check.png') no-repeat;
    left: 50%; top: -12px;
    margin-left: -12px
  }

  &.prev {
    background: url('../assets/prev.png') no-repeat;
    left: 0;
  }

  &.next {
    background: url('../assets/next.png') no-repeat;
    right: 0;
  }

  &:hover {
    filter: brightness(110%);
    transform: scale(1.2);
  }
}
</style>
