<script>
import axios from "axios";
import SelectedPokemon from "./SelectedPokemon.vue";

export default {
  components: { SelectedPokemon },
  data() {
    return {
      pokemonList: [],
      selectedPokemon: null,
      typeEffectiveness: {},
    };
  },
  methods: {
    async getPokemonList() {
      try {
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
          this.pokemonList = pokemonDetailsResponses.map(
            (detail) => detail.data
          );
        }
      } catch (error) {
        console.error("Error fetching Pokémon list:", error);
      }
    },
    async selectedPokemonInfo(id) {
      try {
        const response = await axios.get(
          `https://pokeapi.co/api/v2/pokemon/${id}/`
        );
        if (response.status === 200) {
          this.selectedPokemon = await this.processPokemonData(response.data);
        }
      } catch (error) {
        console.error("Error fetching Pokémon info:", error);
      }
    },
    async processPokemonData(data) {
      const speciesResponse = await axios.get(data.species.url);
      const speciesData = speciesResponse.data;
      const typeEffectiveness = await this.getTypeEffectiveness(
        data.types.map((typeInfo) => typeInfo.type.name)
      );
      const evolutionChainUrl = speciesData.evolution_chain.url;
      let evolutionData = [];
      if (evolutionChainUrl) {
        const evolutionResponse = await axios.get(evolutionChainUrl);
        evolutionData = this.extractEvolutionData(evolutionResponse.data);
      }

      return {
        ...data,
        description: speciesData.flavor_text_entries.find(
          (entry) => entry.language.name === "en"
        ).flavor_text,
        types: data.types.map((type) => type.type.name),
        abilities: data.abilities.map((ability) => ability.ability.name),
        height: data.height / 10,
        weight: data.weight / 10,
        baseExperience: data.base_experience,
        stats: data.stats.reduce((acc, stat) => {
          acc[stat.stat.name] = stat.base_stat;
          return acc;
        }, {}),
        evolutions: evolutionData,
        weaknesses: this.calculateWeaknesses(typeEffectiveness),
      };
    },
    async getTypeEffectiveness(types) {
      const typeEffectiveness = {};
      for (const type of types) {
        try {
          const response = await axios.get(
            `https://pokeapi.co/api/v2/type/${type}/`
          );
          if (response.status === 200) {
            const data = response.data;
            typeEffectiveness[type] = data.damage_relations;
          }
        } catch (error) {
          console.error(
            `Error fetching type effectiveness for ${type}:`,
            error
          );
        }
      }
      return typeEffectiveness;
    },
    calculateWeaknesses(typeEffectiveness) {
      const weaknesses = new Set();

      Object.keys(typeEffectiveness).forEach((type) => {
        const effectiveness = typeEffectiveness[type];
        if (effectiveness.double_damage_from) {
          effectiveness.double_damage_from.forEach((typeInfo) => {
            weaknesses.add(typeInfo.name);
          });
        }
      });

      return Array.from(weaknesses);
    },
    extractEvolutionData(evolutionChain) {
      const evolutions = [];
      let currentChain = evolutionChain.chain;

      while (currentChain) {
        const species = currentChain.species;
        evolutions.push({
          name: species.name,
          url: `https://pokeapi.co/api/v2/pokemon/${species.name}/`,
          minLevel:
            currentChain.evolution_details.length > 0
              ? currentChain.evolution_details[0].min_level
              : null,
        });
        currentChain = currentChain.evolves_to[0];
      }
      return evolutions;
    },
  },
  beforeMount() {
    this.getPokemonList();
    this.selectedPokemonInfo(1);
  },
};
</script>

<template>
  <div class="container">
    <!-- <p v-if="isLoading">Loading...</p> -->
    <div class="row">
      <div class="col-md-8">
        <div class="grid-container">
          <div
            v-for="pokemon in pokemonList"
            :key="pokemon?.id"
            class="pokemon-card"
            @click="selectedPokemonInfo(pokemon?.id)"
          >
            <img
              :src="pokemon?.sprites.other.home.front_default"
              :alt="pokemon?.name"
              class="image"
            />
            <p>{{ pokemon.name }}</p>
          </div>
        </div>
      </div>
      <div class="col-md-4">
        <SelectedPokemon :data="selectedPokemon" />
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
  max-width: 200px;
}
</style>
