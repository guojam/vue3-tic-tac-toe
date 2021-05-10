<template>
    <button class="square">{{ item }}</button>
</template>

<script lang="ts">
import { defineComponent } from 'vue';
export default defineComponent({
    props: {
        item: String,
    },
});
</script>
