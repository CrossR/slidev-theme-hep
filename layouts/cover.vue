<script setup lang="ts">
import { computed } from "vue";
import SlidevConfig from "/@slidev/configs";

const props = defineProps({
  authors: {
    type: String,
    default: "",
  },
  meeting: {
    type: String,
    default: "",
  },
  preTitle: {
    type: String,
    default: "",
  },
  preSub: {
    type: String,
    default: "",
  },
  preDate: {
    type: String,
    default: new Date().toLocaleDateString(),
  },
});

console.log("title:", props.preTitle);
console.log("subTitle:", props.preSub);
console.log("meeting:", props.meeting);
console.log("authors:", props.authors);

// Process authors from dictionary to author name as key, and author affiliation as value
// First, check if there are any authors
</script>
<template>
  <div class="slidev-layout cover" :style="style">
    <div class="my-auto w-full">
      <div
        class="block_back"
        :style="{ background: SlidevConfig.themeConfig.primary }"
      ></div>

      <div class="title_headers">
        <h1>{{ props.preTitle }}</h1>
        <h2>{{ props.preSub }}</h2>
        <div class="title_gap"></div>
        <h3>{{ props.authors }}</h3>
        <h4>{{ props.preDate }}</h4>
      </div>

      <slot> </slot>
    </div>
  </div>
</template>
