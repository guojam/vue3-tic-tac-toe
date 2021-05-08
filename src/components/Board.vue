<template>
    <div class="board-wrap">
        <Square
            v-for="(item, index) in squares"
            :item="item"
            :index="index"
            @click="handleClick(index)"
        />
    </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue';
import Square from './Square.vue';
export default defineComponent({
    name: 'board',
    components: {
        Square,
    },
    props: {
        squares: Array,
    },
    setup: (props, context) => {
        const handleClick = (i: number) => {
            context.emit('square-click', i);
        };

        return { handleClick };
    },
});
</script>
