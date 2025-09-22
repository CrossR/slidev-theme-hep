<script setup lang="ts">
import { computed } from "vue";
import SlidevConfig from "/@slidev/configs";

const props = defineProps({
  authors: {
    type: [String, Array] as () => string | Array<Record<string, string[]>>,
    default: () => [],
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

// If there are authors, store the first in "SlidevConfig.mainAuthor"
if (typeof props.authors === 'string') {
  SlidevConfig.mainAuthor = props.authors;
} else if (Array.isArray(props.authors) && props.authors.length > 0) {
  // Get the first author from the array structure
  const firstAuthorObj = props.authors[0];
  const firstAuthorName = Object.keys(firstAuthorObj)[0];
  SlidevConfig.mainAuthor = firstAuthorName;
}

// Process authors for display
const formattedAuthors = computed(() => {
  // Single author case (string)
  if (typeof props.authors === 'string') {
    console.log("Single author:", props.authors);
    return props.authors;
  }

  // Multiple authors case (array)
  if (Array.isArray(props.authors) && props.authors.length > 0) {
    // Extract author-affiliation pairs from the array structure
    const authorAffiliationPairs: Array<[string, string]> = [];

    props.authors.forEach(authorObj => {
      Object.entries(authorObj).forEach(([name, affiliations]) => {
        // Take the first affiliation if it's an array
        const affiliation = Array.isArray(affiliations) ? affiliations[0] : affiliations;
        authorAffiliationPairs.push([name, affiliation]);
      });
    });

    // Create mapping of affiliations to superscript numbers
    const uniqueAffiliations = [...new Set(authorAffiliationPairs.map(([, affiliation]) => affiliation))];
    const affiliationToNumber = uniqueAffiliations.reduce((acc, affiliation, index) => {
      acc[affiliation] = index + 1;
      return acc;
    }, {} as Record<string, number>);

    // Build author string with superscripts
    return authorAffiliationPairs
      .map(([name, affiliation]) => `${name}<sup>${affiliationToNumber[affiliation]}</sup>`)
      .join(', ');
  }

  return '';
});

// Process affiliations for display
const formattedAffiliations = computed(() => {
  // Only show affiliations for multiple authors case
  if (Array.isArray(props.authors) && props.authors.length > 0) {
    // Extract all affiliations
    const allAffiliations: string[] = [];
    props.authors.forEach(authorObj => {
      Object.entries(authorObj).forEach(([name, affiliations]) => {
        const affiliation = Array.isArray(affiliations) ? affiliations[0] : affiliations;
        allAffiliations.push(affiliation);
      });
    });

    // Get unique affiliations in order
    const uniqueAffiliations = [...new Set(allAffiliations)];

    // Build affiliations string with superscripts
    return uniqueAffiliations
      .map((affiliation, index) => `<sup>${index + 1}</sup> ${affiliation}`)
      .join(', ');
  }

  return '';
});

</script>
<template>
  <div class="slidev-layout cover">
    <div class="my-auto w-full">
      <div class="block_back" :style="style"></div>

      <div class="title_headers">
        <h1>{{ SlidevConfig.title }}</h1>
        <h2 v-if="props.titleSub">{{ props.titleSub }}</h2>
        <h3 v-if="formattedAuthors" v-html="formattedAuthors"></h3>
        <h4 v-if="props.meeting">{{ props.meeting }}</h4>
        <h4>{{ props.talkDate }}</h4>
      </div>

      <div class="title_footers">
        <h5 v-if="formattedAffiliations" v-html="formattedAffiliations"></h5>
      </div>

      <slot> </slot>
    </div>
  </div>
</template>
