<template>
  <div class="home">
    <h3>You are in the dogs' team.</h3>
    <h3><b>Mission:</b> Defeat the lions by dominating the island.</h3>
    <form @submit.prevent="">
      <label for="seed">Game seed:</label>
      <input
        type="number"
        name="seed"
        v-model="seed"
        min="0"
        max="1000000000"
      />
      <button class="secondary" @click="startNewGame">New game</button>
      <button @click="startNewRandomGame">New random game</button>
    </form>
    <div
      v-for="(row, row_index) in board"
      :key="row_index"
      class="d-flex justify-content-center"
    >
      <b-aspect v-for="(cell, col_index) in row" :key="col_index">
        <div
          class="m-2 square"
          :class="cell == 1 ? 'dog' : 'lion'"
          @click="handleClick(row_index, col_index)"
        ></div>
      </b-aspect>
    </div>

    <div v-if="winner" class="winner-screen">
      <div class="d-flex align-items-center h-100 w-100">
        <div class="dog final-dog"></div>
      </div>
      <div class="fixed-top-left">
        <div class="d-flex align-items-center h-100 w-100">
          <h1 class="m-auto text-white">
            Congrats! You have defeated the lions!!
          </h1>
        </div>
      </div>
    </div>
    <!-- </b-container> -->
  </div>
</template>

<script>
// @ is an alias to /src
import Vue from "vue";
import seedrandom from "seedrandom";

export default {
  created() {
    this.seed = Math.floor(Math.random() * this.MAX_SEED_VALUE);
    this.startNewGame();
  },
  name: "Home",
  data: () => {
    return {
      board: [],
      nrows: 3,
      ncols: 3,
      winningState: [],
      winner: false,
      MAX_SEED_VALUE: 1000000000,
    };
  },
  components: {},
  methods: {
    startNewRandomGame() {
      this.seed = Math.floor(Math.random() * this.MAX_SEED_VALUE);
      this.startNewGame();
    },
    startNewGame() {
      if (this.seed < 0) {
        this.seed = 0;
      }
      if (this.seed > this.MAX_SEED_VALUE) {
        this.seed = this.MAX_SEED_VALUE;
      }
      const rng = seedrandom(this.seed);
      this.board = [];
      for (let i = 0; i < this.nrows; i++) {
        this.board = [
          ...this.board,
          new Array(this.ncols).fill(1).map(() => (rng() >= 0.5 ? 1 : 0)),
        ];
      }
      // De-comment this to win inmediately
      // for (let i = 0; i < this.nrows; i++) {
      //   this.board = [...this.board, new Array(this.ncols).fill(1)];
      // }
      this.winningState = [];
      for (let i = 0; i < this.nrows; i++) {
        this.winningState = [
          ...this.winningState,
          new Array(this.ncols).fill(1),
        ];
      }
      console.log("Winning state:");
      console.log(this.winningState);
    },
    handleClick(row_idx, col_idx) {
      console.log(`row: ${row_idx} col: ${col_idx}`);
      Vue.set(this.board[row_idx], col_idx, 1 - this.board[row_idx][col_idx]);
      this.calcAdjIndexes(row_idx, col_idx).forEach((position) => {
        console.log(`row: ${position.row} col: ${position.col}`);
        // this.board[position.row][position.col] = 1 - this.board[position.row][position.col]
        Vue.set(
          this.board[position.row],
          position.col,
          1 - this.board[position.row][position.col]
        );
      });
      this.winner = this.checkWinner();
      console.log(this.winner);
    },
    calcAdjIndexes(row_idx, col_idx) {
      var AdjIndexes = [];
      // UP
      if (row_idx >= 1) {
        AdjIndexes = [...AdjIndexes, { row: row_idx - 1, col: col_idx }];
      }
      // RIGHT
      if (col_idx <= 1) {
        AdjIndexes = [...AdjIndexes, { row: row_idx, col: col_idx + 1 }];
      }
      // DOWN
      if (row_idx <= 1) {
        AdjIndexes = [...AdjIndexes, { row: row_idx + 1, col: col_idx }];
      }
      // LEFT
      if (col_idx >= 1) {
        AdjIndexes = [...AdjIndexes, { row: row_idx, col: col_idx - 1 }];
      }
      return AdjIndexes;
    },
    checkWinner() {
      // check if both the board and the winning state are the same. (i.e [[1 1 1...],[1 1 1...],...])
      for (let i = 0; i < this.nrows; i++) {
        for (let j = 0; j < this.ncols; j++) {
          if (this.board[i][j] !== this.winningState[i][j]) {
            return false;
          }
        }
      }
      return true;
    },
  },
};
</script>

<style scoped>
.dog {
  background-image: url("../assets/sprites/squared/dog.jpg");
}

.lion {
  background-image: url("../assets/sprites/squared/lion.jpg");
}

.square {
  width: 100px;
  height: 100px;
  background-size: cover;
  cursor: pointer;
  animation-name: fadeIn;
  animation-duration: 2.5s;
  animation-iteration-count: 1;
  animation-fill-mode: forwards;
}

.final-dog {
  margin: auto;
  width: 100px;
  height: 100px;
  background-size: cover;
  animation-name: zoomIn;
  animation-duration: 2.5s;
  animation-iteration-count: 1;
  animation-fill-mode: forwards;
}

.winner-screen {
  width: 100%; /* Full width */
  height: 100%;
  position: fixed; /* Stay in place */
  z-index: 1; /* Sit on top */
  left: 0;
  top: 0;
  /* background-color: rgba(245, 22, 22, 0.959); */
  background-image: url("../assets/sprites/og/forest_og.png");
  background-size: cover;
  animation-name: fadeIn;
  animation-duration: 2.5s;
  animation-iteration-count: 1;
  animation-fill-mode: forwards;
}

.fixed-top-left {
  position: fixed; /* Stay in place */
  left: 0;
  top: 0;
  width: 100%; /* Full width */
  height: 100%;
  animation-name: fadeIn;
  opacity: 0;
  animation-delay: 2.5s;
  animation-duration: 2.5s;
  animation-iteration-count: 1;
  animation-fill-mode: forwards;
}

@keyframes zoomIn {
  from {
    width: 10px;
    height: 10px;
  }
  to {
    width: 300px;
    height: 300px;
  }
}

@media (min-width: 768px) {
  .square {
    width: 150px;
    height: 150px;
  }

  .final-dog {
    width: 350px;
    height: 350px;
  }

  @keyframes zoomIn {
    from {
      width: 50px;
      height: 50px;
    }
    to {
      width: 650px;
      height: 650px;
    }
  }
}

@keyframes fadeIn {
  from {
    opacity: 0;
  }
  to {
    opacity: 1;
  }
}
</style>