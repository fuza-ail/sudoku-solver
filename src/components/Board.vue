<template>
  <div class="board">
    <div v-for="(rows, index) in inputs" :key="index">
      <input type="number" />
    </div>
  </div>
</template>

<script>
export default {
  name: "Board",
  data() {
    return {
      inputs: this.generateArray(),
    };
  },
  methods: {
    generateArray() {
      const len = 9;
      const array = [];

      for (let i = 0; i < len; i++) {
        const row = [];
        for (let j = 0; j < len; j++) {
          row.push({
            name: `${i},${j}`,
          });
        }
        array.push(row);
      }
      return array;
    },
  },
};
</script>

<style lang="css" scoped>
input::-webkit-outer-spin-button,
input::-webkit-inner-spin-button {
  -webkit-appearance: none;
  margin: 0;
}
</style>
