<script setup>
// import WelcomeItem from "./WelcomeItem.vue";
// import DocumentationIcon from "./icons/IconDocumentation.vue";
import PlusThickIcon from "vue-material-design-icons/PlusThick.vue";
</script>

<script>
import randomWords from "random-words";
import { db, auth } from "../firebase/firebaseInit";
import {
  signInWithEmailAndPassword,
  onAuthStateChanged,
  signOut as firebaseSignOut,
} from "firebase/auth";
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
      username: "a@b.com",
      password: "",
      isUserSignedIn: false,
      signInMessage: "",
      userSignInText: "",
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
    async signIn() {
      signInWithEmailAndPassword(auth, this.username, this.password)
        .then((userCredential) => {
          console.log("user is signed in!", userCredential);
          this.isUserSignedIn = true;
          this.signInMessage = "User is signed in!";
        })
        .catch((error) => {
          console.error("Code:", error.code, "Message:", error.message);
          this.signInMessage = "Failed :(";
        });
    },

    async signOut() {
      firebaseSignOut(auth)
        .then(() => {
          console.log("Signout successful");
        })
        .catch((error) => {
          console.error("Signout failed", error.code, error.message);
        });
    },

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
      console.log("Stored successfully! ID is", randomId);
      this.itemsToAdd = [];
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
  mounted() {
    onAuthStateChanged(auth, (user) => {
      if (user) {
        this.userSignInText = `signed-in with user ${user.email}`;
        this.isUserSignedIn = true;
      } else {
        this.userSignInText = "User not signed in.";
        this.isUserSignedIn = false;
      }
    });
  },
};
</script>

script

<template>
  <div>
    <h1>Signed-in user is {{ userSignInText }}</h1>
    <div v-if="!isUserSignedIn">
      <input v-model="username" placeholder="Insert username" type="text" />
      <input v-model="password" placeholder="Insert password" type="password" />
      <button @click="signIn">Sign in!</button>
      <div>{{ signInMessage }}</div>
    </div>
    <div style="display: flex; margin-bottom: 30px" v-else>
      <div style="margin-right: 25px">{{ signInMessage }}</div>
      <button @click="signOut">Sign out</button>
    </div>
    <div v-if="!isUserSignedIn">Sorry, user is not signed in :(</div>
    <div style="margin-top: 30px">
      Insert ID to retrieve Data:
      <input
        v-model="docId"
        @keyup.enter="fetchDoc"
        placeholder="Insert id"
        type="text"
      />
    </div>
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
        Name: {{ newItem.name }} - Value: {{ newItem.value }}
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
.invoice-wrap {
  margin-top: 30px;
}

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
