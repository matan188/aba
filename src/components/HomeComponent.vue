<script setup>
import WelcomeItem from "./WelcomeItem.vue";
import DocumentationIcon from "./icons/IconDocumentation.vue";
import PlusThickIcon from "vue-material-design-icons/PlusThick.vue";
</script>

<script>
import json from "../assets/fixture.json";

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
  components: { PlusThickIcon },
  methods: {},
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
  <div>
    <ul>
      <li v-for="(value, name, index) in items" :key="index">
        {{ name }}: {{ value }}
      </li>
    </ul>
    <div class="invoice-wrap flex flex-column">
      <form @submit.prevent="submitForm" class="invoice-">
        <h1>New document</h1>
        <div class="plus-button">
          <plus-thick-icon />
        </div>
        <div class="input"></div>
      </form>
    </div>
  </div>
</template>

<style lang="scss" scoped>
.plus-button {
  cursor: pointer;
}
</style>
