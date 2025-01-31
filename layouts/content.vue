<script setup lang="ts">
import { computed } from "vue";
import SlidevConfig from "/@slidev/configs";

console.log("SlidevConfig:", SlidevConfig);

// Normal slide, but with a persistent footer
</script>
<template>
  <div class="slidev-layout default" :style="style">
    <div class="my-auto w-full">
      <slot> </slot>

      <div v-if="$nav.currentLayout !== 'cover'" class="footer">
        <div style="padding-top: 6px">{{ SlidevConfig.authors }} - {{ SlidevConfig.title }} - {{ SlidevConfig.talkDate
          }}</div>
      </div>

      <div class="slide-number">
        <SlideCurrentNo /> /
        <SlidesTotal />
      </div>

    </div>
  </div>
</template>
