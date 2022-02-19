<script setup>
// import WelcomeItem from "./WelcomeItem.vue";
// import DocumentationIcon from "./icons/IconDocumentation.vue";
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
      itemsToAdd: [],
      newName: "",
      newValue: "",
    };
  },
  components: { PlusThickIcon },
  methods: {
    addNewItem() {
      if (!this.newName || !this.newValue) {
        return;
      }
      const newItem = {
        name: this.newName,
        value: this.newValue,
      };
      this.itemsToAdd.push(newItem);
      this.newName = "";
      this.newValue = "";
    },
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
  <div>
    <ul>
      <li v-for="(value, name, index) in items" :key="index">
        {{ name }}: {{ value.value }}
      </li>
    </ul>
    <div class="invoice-wrap flex flex-column">
      <form @submit.prevent="submitForm" class="invoice-">
        <h1>New document</h1>
        <div v-for="newItem in itemsToAdd" :key="newItem.name">
          Name: {{ newItem.name }}, Value: {{ newItem.value }}
        </div>
        <span>
          <div class="new-item">
            <span class="field-name">
              <div>Field name</div>
              <input v-model="newName" type="text" />
            </span>
            <span class="field-value">
              <div>Field value</div>
              <input v-model="newValue" type="text" />
            </span>
            <div class="plus-button">
              <button @click="addNewItem">
                <plus-thick-icon />
              </button>
            </div>
          </div>
        </span>
      </form>
    </div>
  </div>
</template>

<style lang="scss" scoped>
.plus-button {
  cursor: pointer;
}

.new-item {
  display: flex;
}

.field-name {
  margin-right: 30px;
}

.plus-button {
  margin-left: 25px;
}
</style>
