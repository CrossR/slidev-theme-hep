<script setup lang="ts">
defineProps<{
  number?: number;
}>();
</script>

<template>
    <li class="ml-0! text-xs whitespace-nowrap">
        <sup class="inline-block mr-1" v-if="number">{{ number }}</sup>
        <div class="inline-block">
            <slot />
        </div>
    </li>
</template>

