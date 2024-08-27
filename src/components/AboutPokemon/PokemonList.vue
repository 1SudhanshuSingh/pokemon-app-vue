<script>
import axios from "axios";
import SelectedPokemon from "./SelectedPokemon.vue";
import PokemonCard from "./PokemonCard.vue";

export default {
  components: { SelectedPokemon, PokemonCard },
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
          console.log(await this.processPokemonData(response.data));
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
        gender: await this.getGender(data?.id) || "NA",
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
    async getGender(id) {
      let gender = "";
      try {
        const response = await axios.get(
          `https://pokeapi.co/api/v2/gender/${id}/`
        );
        if (response.status === 200) {
          const data = response.data;
          gender = data?.name;
        }
      } catch (error) {
        console.log(`Error fetching type gender for ${id}:`, error);
      }
      return gender;
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
  <div class="container mt-4">
    <!-- <p v-if="isLoading">Loading...</p> -->
    <div class="col-md-8">
      <div><h1>Hello</h1></div>
      <div><h1>Hello</h1></div>
      <div><h1>Hello</h1></div>
    </div>
    <div class="row">
      <div class="col-md-8 d-grid-3">
        <PokemonCard
          v-for="list in pokemonList"
          :listItem="list"
          :key="list.id"
          @onClick="selectedPokemonInfo"
        />
      </div>
      <div class="col-md-4">
        <SelectedPokemon :data="selectedPokemon" />
      </div>
    </div>
  </div>
</template>
<style scoped></style>
