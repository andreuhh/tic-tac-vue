<!-- implementare winner draw row -->
<template>
  <div class="container">
    <h1>Vue tic tac toe</h1>

    <transition name="bounce" mode="out-in">
      <h2 v-if="winner">
        Winner: <span class="text-success">{{ winner }}</span>
      </h2>
      <h2 v-else>Players-move: {{ player }}</h2>
    </transition>

    <button @click="reset" class="btn btn-primary mb-3">Reset</button>

    <div v-for="(_, x) in 3" :key="x" class="row">
      <button v-for="(_, y) in 3" :key="y" class="square" @click="move(x, y)">
        {{ squares[x][y] }}
      </button>
    </div>

    <h2 class="mt-5">History</h2>
    <transition-group tag="ul" name="list">
      <div v-for="(game, idx) in history" :key="idx">Game: {{ game }} won</div>
    </transition-group>
  </div>
</template>

<script>
import { computed, onMounted, ref, watch } from "vue";

const calculateWinner = (squares) => {
  const lines = [
    [0, 1, 2],
    [3, 4, 5],
    [6, 7, 8],
    [0, 3, 6],
    [1, 4, 7],
    [2, 5, 8],
    [0, 4, 8],
    [2, 4, 6],
  ];
  for (let i = 0; i < lines.length; i++) {
    const [a, b, c] = lines[i];
    if (squares[a] && squares[a] === squares[b] && squares[a] === squares[c]) {
      return squares[a];
    }
  }
  return null;
};

export default {
  setup() {
    const player = ref("X");
    const squares = ref([
      ["", "", ""],
      ["", "", ""],
      ["", "", ""],
    ]);
    const winner = computed(() => calculateWinner(squares.value.flat()));

    // managing users moves
    const move = (x, y) => {
      if (winner.value) {
        console.log("winner");
        return;
      }
      squares.value[x][y] = player.value;
      player.value = player.value === "O" ? "X" : "O";
    };

    const reset = () => {
      player.value = "X";
      squares.value = [
        ["", "", ""],
        ["", "", ""],
        ["", "", ""],
      ];
    };

    const history = ref([]);
    watch(winner, (current, previous) => {
      if (current && !previous) {
        history.value.push(current);
        localStorage.setItem("history", JSON.stringify(history.value));
      }
    });

    onMounted(() => {
      history.value = JSON.parse(localStorage.getItem("history")) ?? [];
    });

    return { winner, player, squares, move, reset, history };
  },
};
</script>

<style scoped>
.bounce-enter-active {
  animation: bounce-in 0.5s;
}
.bounce-leave-active {
  animation: bounce-in 0.5s reverse;
}
@keyframes bounce-in {
  0% {
    transform: scale(0);
  }
  50% {
    transform: scale(1.25);
  }
  100% {
    transform: scale(1);
  }
}

.list-enter-active,
.list-leave-active {
  transition: all 0.5s ease;
}

.list-enter-from {
  opacity: 0;
  transform: translateX(-30px);
}

.list-leave-to {
  opacity: 0;
  transform: translateX(30px);
}

.square {
  background: #fff;
  border: 1px solid #999;
  float: left;
  font-size: 70px;
  font-weight: bold;
  line-height: 34px;
  margin-right: -1px;
  margin-top: -1px;
  padding: 0;
  text-align: center;
  width: 150px;
  height: 150px;
}
</style>