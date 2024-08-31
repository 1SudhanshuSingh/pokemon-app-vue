<script>
import axios from "axios";
import SelectedPokemon from "./SelectedPokemon.vue";
import PokemonCard from "./PokemonCard.vue";
import TheSearchbar from "../UI/TheSearchbar.vue";
import TheFilter from "../UI/TheFilter.vue";

export default {
  components: { SelectedPokemon, PokemonCard, TheSearchbar, TheFilter },
  data() {
    return {
      pokemonList: [],
      selectedPokemon: null,
      sortOrder: "asc",
      typeEffectiveness: {},
      fromPage: 0,
      toPage: 15,
    };
  },
  watch: {
    fromPage() {
      this.getPokemonList();
    },
    toPage() {
      this.getPokemonList();
    },
  },
  computed: {
    sortedPokemonList() {
      return [...this.pokemonList].sort((a, b) => {
        return this.sortOrder === "asc" ? a.id - b.id : b.id - a.id;
      });
    },
  },
  methods: {
    async handleSearch(query) {
      if (query.trim().length !== 0) {
        try {
          const response = await axios.get(
            `https://pokeapi.co/api/v2/pokemon/${query.toLowerCase()}`
          );
          if (response.status === 200) {
            const pokemonData = response.data;
            this.pokemonList = [pokemonData];
          }
        } catch (error) {
          console.error(`Error fetching data for ${query}:`, error);
          this.pokemonList = [];
        }
      } else {
        this.getPokemonList();
      }
    },
    async getPokemonList() {
      try {
        const response = await axios.get(
          `https://pokeapi.co/api/v2/pokemon/?offset=${
            this.fromPage
          }&limit=${15}`
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
          this.selectedPokemon = await this._processPokemonData(response.data);
        }
      } catch (error) {
        console.error("Error fetching Pokémon info:", error);
      }
    },
    async _processPokemonData(data) {
      const speciesResponse = await axios.get(data.species.url);
      const speciesData = speciesResponse.data;
      const typeEffectiveness = await this._getTypeEffectiveness(
        data.types.map((typeInfo) => typeInfo.type.name)
      );
      const evolutionChainUrl = speciesData.evolution_chain.url;
      let evolutionData = [];
      if (evolutionChainUrl) {
        const evolutionResponse = await axios.get(evolutionChainUrl);
        evolutionData = this._extractEvolutionData(evolutionResponse.data);
      }

      return {
        ...data,
        description: speciesData.flavor_text_entries.find(
          (entry) => entry.language.name === "en"
        ).flavor_text,
        gender: (await this._getGender(data?.id)) || "NA",
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
        weaknesses: this._calculateWeaknesses(typeEffectiveness),
      };
    },
    async _getGender(id) {
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
    async _getTypeEffectiveness(types) {
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
    _calculateWeaknesses(typeEffectiveness) {
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
    _extractEvolutionData(evolutionChain) {
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
    updateSortOrder(order) {
      this.sortOrder = order;
    },
    handlePagination(fromPage, toPage) {
      this.fromPage = fromPage;
      this.toPage = toPage;
      this.getPokemonList();
    },
    handleNext(fromPage, toPage) {
      this.fromPage = fromPage;
      this.toPage = toPage;
      this.getPokemonList();
    },
    handlePrev(fromPage, toPage) {
      this.fromPage = fromPage;
      this.toPage = toPage;
      this.getPokemonList();
    },
    handleFilters(filters) {
      const filterKeys = Object.keys(filters).filter(
        (filter) => filters[filter]
      );
      console.log(filters);
      this.pokemonList = [];
      filterKeys.forEach(async (filterKey) => {
        console.log(
          `https://pokeapi.co/api/v2/${filterKey}/${filters[filterKey]}`
        );
        try {
          const response = await axios.get(
            `https://pokeapi.co/api/v2/${filterKey}/${filters[filterKey]}`
          );
          if (response.status === 200) {
            const pokemonData = response.data.pokemon;
            const pokemonDetailsPromises = pokemonData.map((item) =>
              axios.get(item.pokemon.url)
            );
            const pokemonDetailsResponses = await Promise.all(
              pokemonDetailsPromises
            );
            this.pokemonList = pokemonDetailsResponses.map(
              (detail) => detail.data
            );
          }
        } catch (error) {
          console.error(`Error fetching data for ${filterKey}:`, error);
        }
      });
    },
  },
  beforeMount() {
    this.getPokemonList();
    this.selectedPokemonInfo(1);
  },
};
</script>

<template>
  <div class="container mt-2">
    <!-- <p v-if="isLoading">Loading...</p> -->
    <div class="col-md-8">
      <TheSearchbar @handleSearch="handleSearch" />
      <TheFilter
        @getOrder="updateSortOrder"
        @handlePagination="handlePagination"
        @handleNext="handleNext"
        @handlePrev="handlePrev"
        @getAppliedFilter="handleFilters"
      />
    </div>
    <div class="row">
      <div class="col-md-8 row pl--30">
        <PokemonCard
          v-for="list in sortedPokemonList"
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
