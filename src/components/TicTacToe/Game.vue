<template>
    <div class="game">
        <div class="game-board">
            <Board :squares="current.squares" @square-click="handleClick" />
        </div>
        <div class="game-info">
            <div>{{ status }}</div>
            <ol>
                <li
                    v-for="(item, index) in moves"
                    :class="{ current: item.move === stepNumber }"
                >
                    <button @click="jumpTo(item.move)">{{ item.desc }}</button>
                    <span v-if="item.step.currentMove">
                        ({{ item.step.currentMove.col }},
                        {{ item.step.currentMove.row }}):
                        {{ item.step.currentMove.value }}</span
                    >
                </li>
            </ol>
        </div>
    </div>
</template>

<script lang="ts">
import { computed, defineComponent, reactive, toRefs } from 'vue';
import Board from './Board.vue';
import { calculateLoc, calculateWinner } from './util';
export default defineComponent({
    name: 'game',
    components: {
        Board,
    },
    setup: () => {
        const state = reactive({
            history: [{ squares: Array(9).fill(null) }],
            xIsNext: true,
            stepNumber: 0,
        });

        const current = computed(() => state.history[state.stepNumber]);
        const status = computed(() => {
            const squares = current.value.squares,
                winner = calculateWinner(squares);
            if (!squares.includes(null)) {
                return 'Game Over';
            }
            if (winner) {
                return 'Winner: ' + winner;
            }
            return 'Next player: ' + (state.xIsNext ? 'X' : 'O');
        });
        const moves = computed(() => {
            return state.history.map((step, move) => {
                const desc = move ? 'Go to move #' + move : 'Go to game start';
                return {
                    step,
                    move,
                    desc,
                };
            });
        });

        const handleClick = (i: number) => {
            const history = state.history.slice(0, state.stepNumber + 1);
            const current = history[history.length - 1];
            const squares = current.squares.slice();
            if (calculateWinner(squares) || squares[i]) {
                return;
            }
            squares[i] = state.xIsNext ? 'X' : 'O';
            const location = calculateLoc(i);
            state.history = history.concat([
                {
                    squares,
                    currentMove: {
                        col: location.col,
                        row: location.row,
                        value: squares[i],
                    },
                },
            ]);
            state.xIsNext = !state.xIsNext;
            state.stepNumber = history.length;
        };

        const jumpTo = (step: number) => {
            state.stepNumber = step;
            state.xIsNext = step % 2 === 0;
        };

        return {
            ...toRefs(state),
            current,
            status,
            moves,
            handleClick,
            jumpTo,
        };
    },
});
</script>

<style>
body {
    font-size: 14px;
    margin: 20px;
}

ol,
ul {
    padding-left: 0;
    list-style-position: inside;
}

.board-wrap {
    display: grid;
    grid-template-columns: 1fr 1fr 1fr;
}

.status {
    margin-bottom: 10px;
}

.square {
    background: #fff;
    border: 1px solid #999;
    font-size: 24px;
    font-weight: bold;
    line-height: 34px;
    height: 34px;
    margin-right: -1px;
    margin-top: -1px;
    padding: 0;
    text-align: center;
    width: 34px;
}

.square:focus {
    outline: none;
}

.game {
    display: flex;
    flex-direction: row;
}

.game-info {
    margin-left: 20px;
    text-align: left;
}

.game-info li {
    margin: 10px 0;
}
.game-info li span {
    margin-left: 10px;
}
.game-info li.current button {
    font-weight: bold;
}
</style>
