<script setup lang="ts">
import { computed } from "vue";
import SlidevConfig from "/@slidev/configs";
import { useSlideContext } from "@slidev/client"

const { $slidev, $nav, $page, $route } = useSlideContext();

// We need to correct the number of slides.
// For most talks, I want the slide number
// to reflect the true number of slides in the talk.
//
// That is, don't include the cover slides, nor 
// any backups.
//
// Getting the total number of slides is easy, lets
// just hijack the $slidev.nav.tocTree.level
// and set level === -1 in the frontmatter of the
// slides we want to hide.
const totalNumberNonHiddenSlides = $slidev.nav.tocTree.filter((slide) => slide.titleLevel !== -1).length;

// Now we need to correct the current slide number, as well.
// This is a bit more tricky, as we need to count the number
// of slides that are not hidden.
const currentSlideNum = computed(() => {
  const slideNum = $slidev.nav.currentPage;
  let tocEntry = $slidev.nav.tocTree[slideNum - 1];

  // If the current slide is hidden, drop back
  // to the previous non-hidden slide.
  let offset = 1;

  while (tocEntry.titleLevel === -1) {
    offset++;

    if (slideNum - offset < 0) {
      return 0;
    }

    tocEntry = $slidev.nav.tocTree[slideNum - offset];

    // If at any point in our backtracking we hit
    // a slide containing "Backup/Hidden", we should
    // just return the slide number as is.
    if (tocEntry.title.includes("Backup") || tocEntry.title.includes("Hidden")) {
      return totalNumberNonHiddenSlides + offset - 1;
    }
  }

  return slideNum - offset;
});

// Normal slide, but with a persistent footer
</script>
<template>
  <div class="slidev-layout default">
    <div class="my-auto w-full">
      <slot> </slot>

      <div v-if="$nav.currentLayout !== 'cover'" class="footer">
        <div style="padding-top: 6px">{{ SlidevConfig.authors }} - {{ SlidevConfig.title }} - {{ SlidevConfig.talkDate
          }}</div>
      </div>

      <div class="slide-number">
        <span>{{ currentSlideNum }}</span> /
        <span>{{ totalNumberNonHiddenSlides }}</span>
      </div>

    </div>
  </div>
</template>
