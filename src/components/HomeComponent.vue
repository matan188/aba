<script setup>
import WelcomeItem from "./WelcomeItem.vue";
import DocumentationIcon from "./icons/IconDocumentation.vue";
</script>

<script>
import json from "../assets/fixture.json";
// import NewHome from "@/components/NewHome.vue";

export default {
  data() {
    return {
      objectOfAttrs: {
        id: "container",
        class: "wrapper",
      },
      items: {},
    };
  },
  components: {
    // NewHome,
  },
  mounted() {
    for (const [key, value] of Object.entries(json.items)) {
      this.items[key] = value;
    }
    console.log("this.items", this.items);
  },
};
</script>

script

<template>
  <!-- <NewHome /> -->
  <WelcomeItem v-for="(value, name, index) in items" :key="index">
    <template #icon>
      <DocumentationIcon />
    </template>
    <template #heading>{{ value }}</template>

    {{ name }}
  </WelcomeItem>
</template>
