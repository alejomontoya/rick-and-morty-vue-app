<template>
  <div id="app">
    <div class="hero is-white is-gradient is-bold">
      <div class="hero-body">
        <h1 class="title">
          <span class="has-text-success">R&M</span>
          <span class="subtitle">Personajes</span>
        </h1>
        <div class="field has-addons is-pulled-right">
          <div class="control">
            <input
              type="text"
              v-model="search"
              placeholder="buscar por nombre"
              class="input is-rounded"
              v-on:keyup.enter="searchData"
            >
          </div>
          <div class="control">
            <button @click="searchData" class="button is-success is-rounded">Buscar</button>
          </div>
        </div>
      </div>
    </div>

    <div class="container">
      <div class="columns is-desktop is-mobile is-table is-multiline is-centered">
        <character
          @showModal="showModal"
          v-for="character in characters"
          :key="character.id"
          v-bind:character="character"
        />
      </div>
      <nav class="pagination" role="navigartion" aria-label="pagination">
        <a class="pagination-previous" @click="changePage(page - 1)">Anterior</a>
        <ul class="pagination-list">
          <li>
            <a class="pagination-link is-current">{{page}}</a>
          </li>
        </ul>
        <a class="pagination-next" @click="changePage(page + 1)">Siguiente</a>
      </nav>
    </div>

    <div class="modal" :class="{'is-active': modal}" v-if="modal">
      <div class="modal-background" @click="modal = false"></div>
      <div class="modal-card">
        <header class="modal-card-head">
          <p class="modal-card-title">Acerca de: {{currentCharacter.name}}</p>
        </header>
        <div class="modal-card-body">
          <strong>Genero:</strong>
          <p>{{currentCharacter.gender}}</p>
          <strong>Estado:</strong>
          <p>{{currentCharacter.status}}</p>
          <strong>Raza:</strong>
          <p>{{currentCharacter.species}}</p>
        </div>

        <footer class="modal-card-foot">
          <button class="button is-danger" @click="modal = false">Cerrar</button>
        </footer>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import Character from "./components/Character";
export default {
  name: "app",
  components: {
    Character
  },
  data() {
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
      let result = axios
        .get(
          `https://rickandmortyapi.com/api/character/?page=${this.page}&name=${
            this.search
          }`
        )
        .then(res => {
          this.characters = res.data.results;
          this.pages = res.data.info.pages;
        })
        .catch(err => {
          console.log(err);
        });
    },
    changePage(page) {
      this.page = page <= 0 || page > this.pages ? this.page : page;
      this.fetch();
      window.scrollTo(500, 0);
    },
    searchData() {
      this.page = 1;
      this.fetch();
    },
    showModal(id) {
      this.fetchOne(id);
    },
    async fetchOne(id) {
      let results = await axios.get(
        `https://rickandmortyapi.com/api/character/${id}/`
      );
      this.currentCharacter = results.data;
      this.modal = true;
    }
  }
};
</script>

<style lang="scss">
</style>
