<template>
  <div class="column pkmn-gallery fade-in">
    <div class="pokeCard" @click="showModal = true">
      <img :src="pokemon.imageLow" class="hvr-grow" :alt="pokemon.name" />
      <p class="title is-5">
        {{ pokemon.name }} - ID: {{ pokemon.nationalPokedexNumber }}
      </p>
      <p class="heading">{{ pokemon.type }}</p>
    </div>
    <div class="pokeModal" v-if="showModal" @close="showModal = false">
      <transition name="modal">
        <div class="modal-mask">
          <div class="modal-wrapper">
            <div class="modal-container">
              <div class="modal-header">
                <slot name="header">
                  <p class="title is-5">{{ pokemon.name }}</p>
                </slot>
              </div>

              <div class="modal-body">
                <slot name="body">
                  <img
                    :src="pokemon.image"
                    class="hvr-grow"
                    :alt="pokemon.name"
                  />
                  <p>
                    <strong>Pokedex ID:</strong>
                    {{ pokemon.nationalPokedexNumber }}
                  </p>
                  <p><strong>Tipo: </strong>{{ pokemon.type }}</p>
                  <hr />
                  <p>
                    <strong>Resistencia(s):</strong> {{ pokemon.resistances }}
                  </p>
                  <p><strong>Fraqueza(s):</strong> {{ pokemon.weaknesses }}</p>
                </slot>
              </div>

              <div class="modal-footer">
                <button class="button is-link" @click="showModal = false">
                  Fechar
                </button>
              </div>
            </div>
          </div>
        </div>
      </transition>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  created: function() {
    axios.get("https://api.pokemontcg.io/v1/cards/" + this.id).then((res) => {
      this.pokemon.name = res.data.card.name;
      this.pokemon.id = res.data.card.id;
      this.pokemon.nationalPokedexNumber = res.data.card.nationalPokedexNumber;
      this.pokemon.image = res.data.card.imageUrlHiRes;
      this.pokemon.imageLow = res.data.card.imageUrl;
      if (res.data.card.weaknesses)
        this.pokemon.weaknesses = res.data.card.weaknesses[0].type;
      if (res.data.card.types) this.pokemon.type = res.data.card.types[0];
      if (res.data.card.resistances)
        this.pokemon.resistances = res.data.card.resistances[0].type;
      console.log(this.pokemon);
    });
  },
  data() {
    return {
      pokemon: {
        name: "",
        nationalPokedexNumber: "",
        type: "",
        image: "",
        weaknesses: "",
      },
      showModal: "",
    };
  },
  props: {
    nationalPokedexNumber: Number,
    name: String,
    imageUrl: String,
    imageUrlHiRes: String,
    id: String,
    type: String,
    resistances: [
      {
        type: String,
      },
    ],
    weaknesses: [
      {
        type: String,
      },
    ],
  },
};
</script>

<style lang="scss" scoped>
.column {
  display: inline-grid !important;
}
.pkmn-gallery {
  display: inline-grid !important;

  img {
    background-size: 95%;
    margin: 10px 20px 10px 0;
    width: 240px;
    cursor: pointer;
  }

  .pokeCard p {
    color: #FFF;
  }

  .pokeModal p {
    color: #222;
  }
}
.modal-mask {
  position: fixed;
  z-index: 9998;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  display: table;
  transition: opacity 0.3s ease;
}

.modal-wrapper {
  display: table-cell;
  vertical-align: middle;
}

.modal-container {
  width: 360px;
  margin: 0px auto;
  padding: 20px 30px;
  background-color: #fff;
  border-radius: 2px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.33);
  transition: all 0.3s ease;
  font-family: Helvetica, Arial, sans-serif;
}

.modal-header h3 {
  margin-top: 0;
  color: #42b983;
}

.modal-body {
  margin: 20px 0;
}

.modal-default-button {
  float: right;
}
.modal-enter {
  opacity: 0;
}

.modal-leave-active {
  opacity: 0;
}

.modal-enter .modal-container,
.modal-leave-active .modal-container {
  -webkit-transform: scale(1.1);
  transform: scale(1.1);
}
</style>
