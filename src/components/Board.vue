<template>
  <div class="board">
    <div v-for="(rows, indexRow) in inputs" :key="indexRow">
      <input
        min="1"
        max="9"
        type="number"
        maxlength="1"
        v-for="(col, indexCol) in rows"
        :key="indexCol"
        v-model="col.val"
      />
    </div>
    <button @click="generateSolution">test</button>
  </div>
</template>

<script>
export default {
  name: "Board",
  data() {
    return {
      inputs: this.generateArray(),
      solution: [],
      step: 0,
      totalStep: 0,
      isSudokuValid: true,
    };
  },
  watch: {
    step: function (val) {
      console.log("watch:", val);
    },
  },
  methods: {
    generateSolution() {
      this.solve(this.transformVal());
      console.log(this.step);
    },
    generateArray() {
      const len = 9;
      const array = [];

      for (let i = 0; i < len; i++) {
        const row = [];
        for (let j = 0; j < len; j++) {
          row.push({
            val: "",
          });
        }
        array.push(row);
      }
      return array;
    },
    transformVal() {
      const transformed = [];
      this.inputs.forEach((element) => {
        let temp = [];
        element.forEach((el) => {
          temp.push(Number(el.val));
        });
        transformed.push(temp);
      });

      return transformed;
    },
    findEmptyValue(board) {
      for (var i = 0; i < 9; i++) {
        for (var j = 0; j < 9; j++) {
          if (board[i][j] === 0) {
            return [i, j];
          }
        }
      }
      return [-1, -1];
    },
    validateAll(indexRow, indexCol, board, val) {
      const row = board[indexRow];
      const column = board.map((row) => row[indexCol]);
      const box = this.generateArrayBox(indexRow, indexCol, board);

      const isRowValid = this.validate(val, row);
      const isColValid = this.validate(val, column);
      const isBoxValid = this.validate(val, box);

      if (isRowValid && isColValid && isBoxValid) {
        return true;
      }

      return false;
    },
    validate(val, row) {
      return !row.includes(val);
    },
    generateArrayBox(indexRow, indexCol, board) {
      const startRow = Math.floor(indexRow / 3) * 3;
      const startCol = Math.floor(indexCol / 3) * 3;
      const box = [];

      for (let i = startRow; i < 3 + startRow; i++) {
        for (let j = startCol; j < 3 + startCol; j++) {
          box.push(board[i][j]);
        }
      }
      return box;
    },
    solve(board) {
      this.solution = board;
      let emptyVal = this.findEmptyValue(board);
      let row = emptyVal[0];
      let col = emptyVal[1];

      if (row === -1) {
        return board;
      }

      for (let val = 1; val <= 9; val++) {
        if (this.validateAll(row, col, board, val)) {
          board[row][col] = val;
          this.solution[row][col] = val;
          // recursive solve
          this.step++;
          this.totalStep++;
          this.solve(board);
        }
      }

      // if not valid which means no solution, reset to zero
      if (this.findEmptyValue(board)[0] !== -1) {
        this.step--;
        board[row][col] = 0;
        this.solution[row][col] = 0;
      }

      if (this.step <= 0) {
        this.isSudokuValid = false;
      }

      return board;
    },
    sleep(milisecond) {
      var start = new Date().getTime();
      for (var i = 0; i < 1e7; i++) {
        if (new Date().getTime() - start > milisecond) {
          break;
        }
      }
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
input {
  width: 2rem;
  height: 2rem;
  margin: 2px;
  background-color: white;
  text-align: center;
  font-size: 1.2rem;
}

@media only screen and (max-width: 600px) {
  input {
    width: 1.2rem;
    height: 1.2rem;
    margin: 2px;
    background-color: white;
    text-align: center;
    font-size: 0.8rem;
  }
}
</style>
