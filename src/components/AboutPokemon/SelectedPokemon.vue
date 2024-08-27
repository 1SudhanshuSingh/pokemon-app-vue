<script>
import axios from "axios";

export default {
  props: {
    data: {
      type: Object,
      required: false,
      default: () => null,
    },
  },
  data() {
    return {
      gifPokemon: null,
    };
  },
  methods: {
    async getPokemonImageByName(pokemonName) {
      try {
        const response = await axios.get(
          `https://pokeapi.co/api/v2/pokemon/${pokemonName}`
        );
        if (response.status === 200) {
          const data = response.data;
          const animatedGifUrl =
            data.sprites.versions["generation-v"]["black-white"]["animated"]
              .front_default;
          if (animatedGifUrl) {
            return animatedGifUrl;
          }
          const staticPngUrl = data.sprites.home.front_default;
          if (staticPngUrl) {
            return staticPngUrl;
          } else {
            throw new Error("No image available for this Pokémon.");
          }
        } else {
          throw new Error("Failed to fetch Pokémon data.");
        }
      } catch (error) {
        console.error("Error fetching Pokémon image:", error.message);
        return null;
      }
    },
  },
  watch: {
    "data.name": {
      async handler(newName) {
        if (newName) {
          this.gifPokemon = await this.getPokemonImageByName(newName);
        }
      },
      immediate: true,
    },
  },
};
</script>
<template>
  <div class="card-container">
    <div>
      <img
        :src="data?.sprites.other.home.front_default"
        alt=""
        srcset=""
        class="img-fluid pokemon-image"
      />
      <div class="text-center">
        <p class="pokemon-id">#{{ data?.id }}</p>
        <p>{{ data?.gender }}</p>
      </div>
    </div>
    <div>
      <div>
        <p>{{ data?.name }}</p>
      </div>
      <div>
        <p v-for="(type, idx) in data?.types" :key="idx">{{ type }}</p>
      </div>
      <div>
        <p>Pokedex Entery</p>
        <p>
          {{ data?.description }}
        </p>
      </div>
      <div>
        <p>Abilities</p>
        <div>
          <p v-for="(ability, idx) in data?.abilities" :key="idx">
            {{ ability }}
          </p>
        </div>
      </div>
      <div>
        <div>
          <p>Height</p>
          <p>1.7m</p>
        </div>
        <div>
          <p>Weight</p>
          <p>100Kg</p>
        </div>
      </div>
      <div>
        <div>
          <p>Weakness</p>
          <p v-for="(weakness, idx) in data?.weaknesses" :key="idx">
            {{ weakness }}
          </p>
        </div>
        <div>
          <p>Base Exp</p>
          <p>293</p>
        </div>
        <div>
          <p>Stats</p>
          <div>
            <div v-for="(stat, value, idx) in data?.stats" :key="idx">
              <p>{{ stat }}</p>
              <p>{{ value }}</p>
            </div>
          </div>
        </div>
        <div>
          <p>Evolution</p>
          <div v-for="(evolution, index) in data?.evolutions" :key="index">
            <p>
              {{ evolution.name }} - Level {{ evolution.minLevel || "N/A" }}
            </p>
          </div>
        </div>
        <div>
          <div>
            <i>Icon Left</i>
            <img src="" alt="" srcset="" />
            <p>Prinpulp</p>
            <p>#394</p>
          </div>
          <div>
            <p>#394</p>
            <p>Prinpulp</p>
            <img src="" alt="" srcset="" />
            <i>Icon Right</i>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
<style scoped>
.card-container {
  padding: 10rem 1rem 1rem 1rem;
  background: #fff;
  margin-left: 2rem;
  border-radius: 2rem;
  position: relative;
}
.pokemon-image {
  position: absolute;
  top: -18rem;
  left: 0;
}
.pokemon-id{
  color: #959595;
  font-size: 1.25rem;
}
</style>
