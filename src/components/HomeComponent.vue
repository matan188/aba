<script setup>
// import WelcomeItem from "./WelcomeItem.vue";
// import DocumentationIcon from "./icons/IconDocumentation.vue";
import PlusThickIcon from "vue-material-design-icons/PlusThick.vue";
</script>

<script>
import json from "../assets/fixture.json";
import randomWords from "random-words";
import { db } from "../firebase/firebaseInit";
import { collection, addDoc, doc, setDoc } from "firebase/firestore";

export default {
  data() {
    return {
      items: {},
      itemsToAdd: [],
      newName: "",
      newValue: "",
    };
  },
  computed: {
    generatedDoc() {
      return this.itemsToAdd.reduce(function (acc, curr) {
        console.log("curr", curr);
        acc[curr.name] = curr.value;
        return acc;
      }, {});
    },
  },
  components: { PlusThickIcon },
  methods: {
    async addNewItem() {
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

    async storeDoc(docReference, docObject) {
      const docRef = await setDoc(docReference, docObject);
      console.log("Document written with ID: ", docRef);
    },

    async submitDoc() {
      const randomId = this.generateId();
      const docReference = doc(db, "invoices", randomId);
      this.storeDoc(docReference, this.generatedDoc);
      console.log("generated in submitDoc", this.generatedDoc);
    },

    generateId() {
      return `${randomWords()}-${randomWords()}`;
    },

    buildDoc() {},
  },
  mounted() {
    for (const [key, value] of Object.entries(json.items)) {
      this.items[key] = value;
    }
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
      <h1>New document</h1>
      <div>
        <button @click="submitDoc">Submit new doc</button>
      </div>
      <div v-for="newItem in itemsToAdd" :key="newItem.name">
        Name: {{ newItem.name }} | Value: {{ newItem.value }}
      </div>
      <span>
        <div class="new-item">
          <span class="field-name">
            <div>Field</div>
            <input v-model="newName" type="text" />
          </span>
          <span @keyup.enter="addNewItem" class="field-value">
            <div>Value</div>
            <input v-model="newValue" type="text" />
          </span>
          <div class="plus-button">
            <button @click="addNewItem">
              <plus-thick-icon />
            </button>
          </div>
        </div>
      </span>
    </div>
  </div>
</template>

<style lang="scss" scoped>
.plus-button {
  cursor: pointer;

  button {
    cursor: pointer;
  }
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
