<template>
    <button class="square" @click="onClick()">{{ item }}</button>
</template>

<script lang="ts">
import { ref, defineComponent } from 'vue';
export default defineComponent({
    name: 'square',
    props: {
        item: String,
    },
    setup: (props, context) => {
        const onClick = () => {
            context.emit('square-click');
        };
        return { onClick };
    },
});
</script>
