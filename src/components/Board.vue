<template>
  <div class="board">
    <div v-if="!isSudokuValid">
      <p>This sudoku is unsolvable!!!</p>
    </div>
    <div class="content">
      <div v-if="loading" class="loading">
        <p>Please wait...</p>
      </div>
      <div class="container">
        <div v-for="(rows, indexRow) in inputs" :key="indexRow">
          <input
            type="number"
            v-for="(col, indexCol) in rows"
            :key="indexCol"
            :value="col.val"
            @input="handleInput($event, indexRow, indexCol)"
            :disabled="isButtonDisabled"
          />
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "Board",
  data() {
    return {
      inputs: this.generateArray(),
      solution: [
        [0, 0, 0, 0, 0, 0, 0, 0, 0],
        [0, 0, 0, 0, 0, 0, 0, 0, 0],
        [0, 0, 0, 0, 0, 0, 0, 0, 0],
        [0, 0, 0, 0, 0, 0, 0, 0, 0],
        [0, 0, 0, 0, 0, 0, 0, 0, 0],
        [0, 0, 0, 0, 0, 0, 0, 0, 0],
        [0, 0, 0, 0, 0, 0, 0, 0, 0],
        [0, 0, 0, 0, 0, 0, 0, 0, 0],
        [0, 0, 0, 0, 0, 0, 0, 0, 0],
      ],
      step: 0,
      totalStep: 0,
      isSudokuValid: true,
      isButtonDisabled: false,
      isSudokuDone: false,
      loading: false,
      time: 2000,
    };
  },
  methods: {
    handleInput(e, x, y) {
      // console.log(e.target.value.length);
      if (e.target.value.length > 1) {
        const value = e.target.value.substr(e.target.value.length - 1);
        this.inputs[x][y].val = value;
      }
      // console.log(e.target.value, x, y);
    },
    generateSolution() {
      this.solve(this.transformVal());
      this.loading = true;
      setTimeout(() => {
        this.inputs = this.solution.map((element) => {
          let arr = [];
          element.forEach((el) => {
            arr.push({ val: el });
          });
          return arr;
        });
        this.loading = false;
        this.isButtonDisabled = true;
        this.isSudokuDone = true;
      }, this.time);
    },
    reset() {
      this.isButtonDisabled = false;
      this.inputs = this.generateArray();
      this.isSudokuValid = true;
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

      if (this.totalStep > 10000 || this.step < 0) {
        setTimeout(() => {
          this.isSudokuValid = false;
        }, this.time);
        return;
      }

      if (row === -1) {
        return;
      }

      for (let val = 1; val <= 9; val++) {
        if (this.validateAll(row, col, this.solution, val)) {
          // board[row][col] = val;
          this.solution[row][col] = val;
          // recursive solve
          this.step++;
          this.totalStep++;
          this.solve(this.solution);
        }
      }

      // if not valid which means no solution, reset to zero
      if (this.findEmptyValue(this.solution)[0] !== -1) {
        this.step--;
        // board[row][col] = 0;
        this.solution[row][col] = 0;
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

input[disabled] {
  background-color: rgba(128, 128, 128, 0.411);
}

.content {
  position: relative;
  display: inline-block;
}

.container {
  margin: auto;
}
.loading {
  position: absolute;
  width: 100%;
  height: 100%;
  background-color: rgba(99, 99, 99, 0.61);
  z-index: 100;
  display: flex;
  align-items: center;
  justify-content: center;
}
.loading p {
  font-size: 2.2rem;
  color: white;
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
