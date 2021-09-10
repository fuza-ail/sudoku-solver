<template>
  <div class="home">
    <h2>SODOKU SOLVER</h2>
    <board ref="board"></board>
    <br />
    <div>
      <button @click="submit">SUBMIT</button>
      <button @click="reset">RESET</button>
    </div>
  </div>
</template>

<script>
import Board from "../components/Board.vue";

export default {
  name: "Home",
  components: { Board },
  methods: {
    submit() {
      this.$refs.board.generateSolution();
    },
    reset() {
      this.$refs.board.reset();
    },
  },
};
</script>

<style lang="css" scoped>
h2 {
  margin: 0;
  padding: 1rem;
}
button {
  position: relative;
  cursor: pointer;
  margin: 1rem;
  padding: 5px 20px;
  border: 1px black solid;
  background-color: rgb(255, 255, 255);
  transition: all 0.3s ease-in-out;
  z-index: 1;
}
button::before {
  content: "";
  position: absolute;
  display: block;
  height: 100%;
  width: 100%;
  transform: translate(-20px, -5px);
  transition: all 0.3s ease-in-out;
  z-index: -1;
}

button:hover::before {
  background-color: black;
}

button:hover {
  color: white;
}
.home {
  width: 100%;
  height: 100vh;
  background-color: rgb(245, 245, 245);
  z-index: -100;
}
</style>
