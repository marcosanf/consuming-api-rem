<template>
  <div id="app">
    <div class="hero is-white is-gradient is-">
      <div class="hero-body">
        <h1 class="title">
          <span class="has-text-success">The Rick and Morty API</span>
          <span class="subtitle"> search and meet your favorite character</span>
        </h1>
        <div class="field has-addons is-pulled-right">
          <div class="control">
            <input
              v-model="search"
              type="text"
              class="input is-rounded"
              v-on:keyup.enter="searchData"
            >
          </div>
          <div class="control">
            <button class="button is-success is-rounded" v-on:click="searchData">Search</button>
          </div>
        </div>
      </div>
    </div>

    <div class="container">
      <div class="columns is-desktop is-mobile is-tablet is-multiline is-centered">
        <character
          @showModal="showModal"
          v-for="character of characters"
          v-bind:key="character.id"
          v-bind:character="character"
        />
      </div>
    </div>

    <nav class="pagination" role="pagination" aria-label="pagination">
      <a class="pagination-previous" v-on:click="changePage(page-1)">Previous</a>
      <ul class="pagination-list">
        <li>
          <a class="pagination-link is-current">{{page}}</a>
        </li>
      </ul>
      <a class="pagination-next" v-on:click="changePage(page+1)">Next</a>
    </nav>
  </div>
</template>

<script>
//libriries imports
import axios from "axios";

import Character from "./components/Character";

export default {
  name: "App",
  components: {
    Character //use like = character
  },
  data: function() {
    return {
      characters: [],
      page: 1,
      pages: 1,
      search: "",
      modal: false,
      currentCharacter: {}
    };
  },
  created() {
    this.fetch();
  },
  methods: {
    fetch() {
      const params = {
        page: this.page,
        name: this.search
      };
      let result = axios
        //https://rickandmortyapi.com/api/character/?page=<n> n is the page number in API, storage in params variable
        .get("https://rickandmortyapi.com/api/character/", { params })
        .then(res => {
          this.characters = res.data.results;
          this.pages = res.data.info.pages;
          console.log(res.data);
        })
        .catch(err => {
          console.log(err);
        });
    },
    changePage(page) {
      this.page = page <= 0 || page > this.pages ? this.page : page;
      this.fetch();
    },
    searchData () {
      this.page = 1;
      this.fetch();
    },
    showModal (id) {
      this.fetchOne()
    },
    async fetchOne (id) {
      let result = await axios.get(`https://rickandmortyapi.com/api/character/${id}/`);
      this.currentCharacter = result.data;
      this.modal = true;
      console.log(this.currentCharacter, 'Character');
    }
  }
};
</script>

<style>
</style>
