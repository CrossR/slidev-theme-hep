<script setup lang="ts">
import { computed } from "vue";
import SlidevConfig from "/@slidev/configs";
import { useSlideContext } from "@slidev/client"

const { $slidev, $nav, $page, $route } = useSlideContext();

// Custom properties for the default layout, allowing some slight tweaks.
const props = defineProps({
  // Width of the content area.
  width: {
    type: String,
    default: "w-4/5",
  },
  // Name of the person whose work this is.
  author: {
    type: String,
    default: "",
  },
});

// We need to correct the number of slides.
// For most talks, I want the slide number
// to reflect the true number of slides in the talk.
//
// That is, don't include any slides that are "hidden".
// I use the frontmatter to hide slides, like so:
// ---
// level: -1
// ---
//
// This means I can hide covers, backup slides, etc.
// It also means if I have fake duplicated slides
// to bring in annotations or new plots etc, I can
// also hide them.
//
// Because of this, only count slides that have a
// titleLevel that is not -1.
const totalNumberNonHiddenSlides = $slidev.nav.tocTree.filter((slide) => slide.titleLevel !== -1).length;

// Now we need to correct the current slide number, as well.
//
// Broadly, we want to count backwards from the current slide
// incrementing a counter for each slide that is not hidden.
// This should give us the true slide number.
//
// For backup slides that are hidden, we should just return
// the offset from the "Backup" slide to the current slide.
// This means in a talk with "20" slides, and 5 backup slides,
// the backups are slides 21-25.
const currentSlideNum = computed(() => {
  const slideNum = $slidev.nav.currentPage;
  const slideIndex = slideNum - 1;
  let tocEntry = $slidev.nav.tocTree[slideIndex];

  // We are going to backtrack, counting the slide number
  // as we go, but only counting the slides that are not hidden.
  // This should give us the true slide number.
  let trueSlideNumber = 0;
  let indexOffset = 0;

  while (tocEntry.no !== 1) {

    tocEntry = $slidev.nav.tocTree[slideIndex - indexOffset];
    indexOffset++;

    // If this is a non-hidden slide, increment the true slide number.
    if (tocEntry.titleLevel !== -1) {
      trueSlideNumber++;
    }

    // If at any point in our backtracking we hit
    // a slide containing "Backup/Hidden", we should
    // just return the slide number as is.
    if (tocEntry.title.includes("Backup") || tocEntry.title.includes("Hidden")) {
      return totalNumberNonHiddenSlides + indexOffset - 1;
    }
  }

  return trueSlideNumber;
});

// Calculate a general font size to use.
const fontSize = SlidevConfig.themeConfig?.fontSize || '1.1rem';

// Normal slide, but with a persistent footer
</script>
<template>
  <div class="slidev-layout default" :style="`font-size: ${fontSize}`">
    <div class="my-auto" :class="props.width">
      <slot> </slot>

      <!-- If there is an author, display it in the very top left -->
      <div v-if="props.author" class="author">
        <div style="padding-top: 6px">{{ props.author }}</div>
      </div>

      <div v-if="$nav.currentLayout !== 'cover'" class="footer">
        <div style="padding-top: 6px">{{ SlidevConfig.author }} - {{ SlidevConfig.title }} - {{ SlidevConfig.talkDate
        }}</div>
      </div>

      <div class="slide-number">
        <span>{{ currentSlideNum }}</span> /
        <span>{{ totalNumberNonHiddenSlides }}</span>
      </div>

    </div>
  </div>
</template>
