<template>
    <div class="game">
        <div class="game-board">
            <Board
                :squares="current.squares"
                @square-click="handleClick"
                :winnerLine="winnerLine"
            />
        </div>
        <div class="game-info">
            <div>{{ status }}</div>
            <div class="history-sort">
                <label>
                    <input
                        type="radio"
                        name="radio-sort"
                        value="asc"
                        v-model="sort"
                    />ascending</label
                >
                <label>
                    <input
                        type="radio"
                        name="radio-sort"
                        value="desc"
                        v-model="sort"
                    />descending</label
                >
            </div>
            <ul>
                <li
                    v-for="(item, index) in moves"
                    :class="{ current: item.move === stepNumber }"
                    @click="jumpTo(item.move)"
                >
                    step
                    {{ sort === 'asc' ? index + 1 : moves.length - index }}:
                    <span>{{ item.desc }}</span>
                    <span v-if="item.step.currentMove">
                        -- ({{ item.step.currentMove.col }},
                        {{ item.step.currentMove.row }}):
                        {{ item.step.currentMove.value }}
                    </span>
                </li>
            </ul>
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
            winnerLine: [],
            sort: 'asc',
        });

        const current = computed(() => state.history[state.stepNumber]);
        const status = computed(() => {
            const squares = current.value.squares,
                winner = calculateWinner(squares);
            if (!squares.includes(null)) {
                return 'No one wins';
            }
            if (winner) {
                state.winnerLine = winner.loc;
                return 'Winner: ' + winner.value;
            } else {
                state.winnerLine = [];
                return 'Next player: ' + (state.xIsNext ? 'X' : 'O');
            }
        });
        const moves = computed(() => {
            const arr = state.history.map((step, move) => {
                const desc = move ? 'Go to move #' + move : 'Go to game start';
                return {
                    step,
                    move,
                    desc,
                };
            });
            return state.sort === 'desc' ? arr.reverse() : arr;
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

ul {
    padding-left: 0;
    list-style: none;
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
.square.winner-square {
    color: blue;
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
    margin-top: 5px;
}
.game-info li:hover {
    color: blue;
    text-decoration: underline;
}
.game-info li span + span {
    margin-left: 10px;
}
.game-info li.current {
    font-weight: bold;
}
.history-sort {
    margin-top: 15px;
}
.history-sort input {
    margin: 0 3px 0 0;
    vertical-align: middle;
}
.history-sort label + label {
    margin-left: 15px;
}
</style>
