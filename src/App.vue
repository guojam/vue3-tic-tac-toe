<template>
    <Game />
</template>

<script lang="ts">
import { defineComponent } from 'vue';
import Game from './components/TicTacToe/';

export default defineComponent({
    name: 'App',
    components: {
        Game,
    },
});
</script>
