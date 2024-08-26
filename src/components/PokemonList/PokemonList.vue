<script steup>
import axios from "axios";
export default {
  data() {
    return {
      pokemonList: [],
    };
  },
  methods: {
    getPokemonList: async function () {
      const response = await axios.get(
        "https://pokeapi.co/api/v2/pokemon/?offset=0&limit=20"
      );
      if (response.status === 200) {
        const pokemonData = response.data.results;
        const pokemonDetailsPromises = pokemonData.map((pokemon) =>
          axios.get(pokemon.url)
        );
        const pokemonDetailsResponses = await Promise.all(
          pokemonDetailsPromises
        );
        this.pokemonList = pokemonDetailsResponses.map((detail) => ({
          ...detail.data,
          showShiny: false,
        }));
      }
    },
  },
  beforeMount() {
    this.getPokemonList();
  },
};
</script>
<template>
  <div class="container">
    <div class="grid-container">
      <div
        v-for="pokemon in pokemonList"
        :key="pokemon.id"
        class="pokemon-card"
        @mouseenter="showShiny = true"
        @mouseleaver="showShiny = false"
      >
        <img
          v-if="showShiny"
          :src="pokemon.sprites.other.home.front_shiny"
          :alt="pokemon.name"
          class="image"
        />
        <img
          v-else
          :src="pokemon.sprites.other.home.front_default"
          :alt="pokemon.name"
          class="image"
        />
        <p>{{ pokemon.name }}</p>
      </div>
    </div>
  </div>
</template>
<style scoped>
.grid-container {
  margin-top: 2rem;
  display: grid;
  grid-template-columns: auto auto auto;
  text-align: center;
  gap: 2rem;
}
.pokemon-card {
  background-color: #fff;
  border-radius: 3rem;
  box-shadow: 8px 8px 4px #d1d1d1;
}
.pokemon-card p {
    font-size: 1.5rem;
    font-weight: 600;
    text-transform: capitalize;
}
.image {
  width: 100%;
}
</style>
