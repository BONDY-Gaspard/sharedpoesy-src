<script setup>
import SignIn from "./components/SignIn.vue";
import PocketBase from "pocketbase";
</script>

<template>
  <header>
    <router-link to="/">Go to Home</router-link>
    <img
      alt="Logo"
      class="logo"
      src="./assets/logo.svg"
      width="125"
      height="125"
    />
    <div class="wrapper" id="signOut">
      <div><SignIn msg="User, please sign in !" /></div>
      <label>email: </label><br />
      <input
        type="email"
        required
        id="email"
        placeholder="username@domain.tld"
      /><br />
      <label>password: </label><br />
      <input type="password" required id="passwd" /><br />
      <button v-on:click="login()">Sign In</button>
      <button v-on:click="add()">Add</button>
      <button v-on:click="google()">Connexion par google</button>
      <button v-on:click="github()">Connexion par github</button>
      <p><label id="status"> You are not yet connected </label><br /></p>
    </div>
  </header>

  <main></main>
</template>

<script>
var connected = false;
var pocketbase_ip = "";
if (import.meta.env.MODE === "production")
  pocketbase_ip = "https://sharedpoesy.gaspardbondy.fr:443";
else pocketbase_ip = "http://127.0.0.1:8090";
const pb = new PocketBase(pocketbase_ip);
var currentUser;
export default {
  methods: {
    //this method allows a new user to sign up the system. Once done, the user receives an email
    //asking for account validation. Once the validation made the user is added to the system
    async login() {
      await pb.collection("users").authWithOAuth2({ provider: "google" });
      if (pb.authStore.isValid) {
        document.getElementById("status").innerHTML = "You are now logged in";
        connected = true;
        currentUser=pb.authStore.model;
      }
    },
    async add() {
      const record = await pb.collection("poems").create({
        title: "good year",
        content: "how a nice year",
        private: false,
        email:currentUser.email
      });
    },
    //this method allows the already registred user to log in the system.

    async google() {
      await pb.collection("users").authWithOAuth2({ provider: "google" });
      if (pb.authStore.isValid) {
        document.getElementById("status").innerHTML = "You are now logged in";
        connected = true;
        currentUser=pb.authStore.model;
      }
    },

    async github() {
      await pb.collection("users").authWithOAuth2({ provider: "github" });
      if (pb.authStore.isValid) {
        document.getElementById("status").innerHTML = "You are now logged in";
        connected = true;
        currentUser=pb.authStore.model;
      }
    },

  },
};
</script>

<style>
@import "./assets/base.css";

header .hidden {
  visibility: hidden;
  overflow: hidden;
  display: flex;
  display: inline-block;
  place-items: flex-start;
  flex-wrap: wrap;
}

#app {
  max-width: 1280px;
  margin: 0 auto;
  padding: 2rem;

  font-weight: normal;
}

header {
  line-height: 1.5;
}

.logo {
  display: block;
  margin: 0 auto 2rem;
}

a,
.green {
  text-decoration: none;
  color: hsla(160, 100%, 37%, 1);
  transition: 0.4s;
}

@media (hover: hover) {
  a:hover {
    background-color: hsla(160, 100%, 37%, 0.2);
  }
}

@media (min-width: 1024px) {
  body {
    display: flex;
    place-items: center;
  }

  #app {
    display: grid;
    grid-template-columns: 1fr 1fr;
    padding: 0 2rem;
  }

  header {
    display: inline-block;
    place-items: center;
    padding-right: calc(var(--section-gap) / 2);
  }

  header .wrapper {
    display: flex;
    display: inline-block;
    place-items: flex-start;
    flex-wrap: wrap;
  }

  .logo {
    margin: 0 2rem 0 0;
  }
}
</style>
