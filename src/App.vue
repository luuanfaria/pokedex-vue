<template>
  <div id="app">
    <section class="section">
      <div class="container">
        <h1 class="title">Pokedex - VueJs</h1>
        <input
          class="input is-focused"
          type="text"
          placeholder="Busque pelo nome"
          v-model="search"
        />
        <div class="title" v-if="loading" style="margin-top: 20px;">
          Carregando...
        </div>
        <Pokemon
          v-for="(pokemon, index) in searchResult"
          :key="pokemon.id"
          :name="pokemon.name"
          :id="pokemon.id"
          :nationalPokedexNumber="index + 1"
        />
      </div>
    </section>
  </div>
</template>

<script>
import axios from "axios";
import Pokemon from "./components/Pokemon";

export default {
  name: "App",
  data() {
    return {
      pokemons: [],
      search: "",
      loading: true,
    };
  },
  created: function() {
    axios
      .get("https://api.pokemontcg.io/v1/cards?pageSize=1000")
      .then((res) => {
        this.pokemons = res.data.cards;

        this.pokemons = this.pokemons.filter((pokemon) => {
          return pokemon.supertype === "Pokémon";
        });

        this.pokemons.sort(function(a, b) {
          if (a.name < b.name) {
            return -1;
          }
          if (a.name > b.name) {
            return 1;
          }
          return 0;
        });
      })
      .catch((error) => console.log(error))
      .finally(() => (this.loading = false));
  },
  components: {
    Pokemon,
  },
  computed: {
    searchResult: function() {
      if (this.search == "" || this.search == " ") {
        return this.pokemons;
      } else {
        return this.pokemons.filter((pokemon) => pokemon.name == this.search);
      }
    },
  },
};
</script>

<style lang="scss" scoped>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  background: #30a7d7
    url(https://assets.pokemon.com/static2/_ui/img/chrome/container_bg.png);
  min-height: 100vh;
  color: #FFF
}
#root > div .section {
  margin-top: 60px;
  flex-grow: 1;
}
h1 {
  color: #FFF;
}
</style>
