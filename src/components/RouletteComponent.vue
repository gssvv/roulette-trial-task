<template>
  <div>
    <div class="form">
      <p>Время: <input type="text" v-model="time" /></p>
      <p>
        Номер элемента: <input type="text" v-model="itemNum" /> (от 4 до 50)
      </p>
      <button @click="spin()" :disabled="spinActivated">Запустить</button>
      <button @click="reset()">Сбросить</button>
    </div>
    <div class="roulette" ref="rouletteWrapper">
      <div class="roulette-cursor" ref="cursor"></div>
      <div class="roulette-wrapper" ref="roulette">
        <div
          class="roulette-item"
          v-for="i in 50"
          :key="i"
          v-text="i"
          :ref="`rouletteItem`"
        />
      </div>
    </div>
  </div>
</template>

<script>
import RouletteSound from "@/assets/roulette_spin.mp3";

export default {
  data: () => ({
    time: 8000,
    itemNum: 45,
    itemToStartDetecting: 4,
    spinActivated: false
  }),
  mounted() {
    this.detectCursor();
  },
  methods: {
    spin() {
      this.spinActivated = true;
      let { rouletteWrapper } = this.$refs;
      let { roulette } = this.$refs;
      let item = this.$refs.rouletteItem[this.itemNum - 1];

      let rouletteWrapperRect = rouletteWrapper.getBoundingClientRect();
      let rouletteRect = roulette.getBoundingClientRect();
      let itemRect = item.getBoundingClientRect();

      let translateX =
        itemRect.left -
        rouletteRect.left -
        rouletteWrapperRect.width / 2 +
        itemRect.width / 2;

      roulette.style.transition = `all ${this.time}ms cubic-bezier(0.32, 0.64, 0.45, 1)`;
      roulette.style.transform = `translateX(${-translateX}px)`;
    },
    reset() {
      this.spinActivated = false;
      let { roulette } = this.$refs;
      this.time = 8000;
      this.itemNum = 45;
      this.itemToStartDetecting = 4;

      roulette.style.transition = `all 0ms cubic-bezier(0.32, 0.64, 0.45, 1)`;
      roulette.style.transform = `translateX(0px)`;
    },
    detectCursor() {
      if (this.$refs.rouletteItem[this.itemToStartDetecting - 1]) {
        let { cursor } = this.$refs;
        let cursorRect = cursor.getBoundingClientRect();

        let item = this.$refs.rouletteItem[this.itemToStartDetecting - 1];
        let itemRect = item.getBoundingClientRect();

        let entered = cursorRect.left > itemRect.left;
        let left = cursorRect.left > itemRect.left + itemRect.width;

        if (entered && !left) {
          new Audio(RouletteSound).play();
          this.itemToStartDetecting = this.itemToStartDetecting + 1;
        }
      }

      window.requestAnimationFrame(this.detectCursor);
    }
  }
};
</script>

<style lang="sass" scoped>
.roulette
  width: 800px
  margin: 100px auto
  overflow: hidden
  border: 1px solid #ccc
  height: 200px
  position: relative
  &-cursor
    content: ""
    position: absolute
    height: 100%
    width: 2px
    background-color: blue
    opacity: .5
    left: calc(50% - 1px)
    top: 0
    z-index: 10
  &-wrapper
    width: 99999px
    height: 200px
    display: flex
    align-items: center
    transform: translateX(0px)
  &-item
    height: 120px
    width: 120px
    background-color: red
    margin: 0 20px
    display: inline-block
    display: flex
    align-items: center
    justify-content: center
    color: #fff
    font-size: 32px

.form
  width: 800px
  margin: 40px auto
</style>
