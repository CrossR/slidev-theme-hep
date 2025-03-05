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
  titleSub: {
    type: String,
    default: "",
  },
  talkDate: {
    type: String,
    default: new Date().toLocaleDateString(),
  },
});

const style = computed(() => ({
  background: SlidevConfig.themeConfig.primary,
}));

console.log("SlidevConfig:", SlidevConfig);
console.log("subTitle:", props.titleSub);
console.log("meeting:", props.meeting);
console.log("date:", props.talkDate);
console.log("authors:", props.authors);

// If there is multiple authors, store the first
// in the "SlidevConfig.mainAuthor"
SlidevConfig.mainAuthor = props.authors.split(",")[0].trim();

// Process authors from dictionary to author name as key, and author affiliation as value
// First, check if there are any authors
</script>
<template>
  <div class="slidev-layout cover">
    <div class="my-auto w-full">
      <div class="block_back" :style="style"></div>

      <div class="title_headers">
        <h1>{{ SlidevConfig.title }}</h1>
        <h2 v-if="props.titleSub">{{ props.titleSub }}</h2>
        <h3>{{ props.authors }}</h3>
        <h4 v-if="props.meeting">{{ props.meeting }}</h4>
        <h4>{{ props.talkDate }}</h4>
      </div>

      <slot> </slot>
    </div>
  </div>
</template>
