<script setup>
// import WelcomeItem from "./WelcomeItem.vue";
// import DocumentationIcon from "./icons/IconDocumentation.vue";
import PlusThickIcon from "vue-material-design-icons/PlusThick.vue";
</script>

<script>
import randomWords from "random-words";
import { db } from "../firebase/firebaseInit";
import { getDoc, doc, setDoc } from "firebase/firestore";

const COLLECTION_NAME = "invoices";

export default {
  data() {
    return {
      items: {},
      fetchedItems: [],
      itemsToAdd: [],
      newName: "",
      newValue: "",
      docId: "",
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
      await setDoc(docReference, docObject);
    },

    async submitDoc() {
      const randomId = this.generateId();
      const docPointer = doc(db, COLLECTION_NAME, randomId);
      await this.storeDoc(docPointer, this.generatedDoc);
      console.log("Document written with ID: ", randomId);
      console.log("generated in submitDoc", this.generatedDoc);
    },

    generateId() {
      return `${randomWords()}-${randomWords()}`;
    },

    buildDoc(docObject) {
      this.fetchedItems = [];
      for (const [name, value] of Object.entries(docObject)) {
        this.fetchedItems.push({ name, value });
      }
    },

    async fetchDoc() {
      const docRef = doc(db, COLLECTION_NAME, this.docId);
      const docSnap = await getDoc(docRef);

      if (docSnap.exists()) {
        this.buildDoc(docSnap.data());
        this.docId = "";
      } else {
        console.error("fetchedDoc not found");
      }
    },
  },
};
</script>

script

<template>
  <div>
    <input
      v-model="docId"
      @keyup.enter="fetchDoc"
      placeholder="Insert id"
      type="text"
    />
    <ul>
      <li v-for="(currItem, name, index) in fetchedItems" :key="index">
        {{ currItem.name }}: {{ currItem.value }}
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
