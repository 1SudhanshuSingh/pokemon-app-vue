<script>
import axios from "axios";
import Images from "@/constants/images";
import TypeIcons from "@/constants/typeSvg";
import TheTypeButton from "../UI/TheTypeButton.vue";
export default {
  components: { TheTypeButton },
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
      maleIcon: Images.male,
      femaleIcon: Images.female,
      typeIcons: {
        normal: TypeIcons.normal,
        fire: TypeIcons.fire,
        water: TypeIcons.water,
        electric: TypeIcons.electric,
        grass: TypeIcons.grass,
        ice: TypeIcons.ice,
        fighting: TypeIcons.fighting,
        poison: TypeIcons.poison,
        ground: TypeIcons.ground,
        flying: TypeIcons.flying,
        psychic: TypeIcons.psychic,
        bug: TypeIcons.bug,
        rock: TypeIcons.rock,
        ghost: TypeIcons.ghost,
        dragon: TypeIcons.dragon,
        dark: TypeIcons.dark,
        steel: TypeIcons.steel,
        fairy: TypeIcons.fairy,
      },
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
      <div class="ribbon">
        <p>BASE EXP</p>
        <p>{{ data?.baseExperience }}</p>
      </div>
      <img
        :src="data?.sprites.other.home.front_default"
        alt=""
        srcset=""
        class="img-fluid pokemon-image"
      />
      <div class="text-center">
        <p class="pokemon-id">#{{ data?.id }}</p>
        <p class="pokemon-name">{{ data?.name }}</p>
        <div class="pokemon-gender">
          <img v-if="data?.gender === 'female'" :src="femaleIcon" />
          <img v-if="data?.gender === 'male'" :src="maleIcon" />
        </div>
      </div>
    </div>
    <div>
      <div class="text-center d-flex">
        <TheTypeButton
          v-for="(type, idx) in data?.types"
          :key="idx"
          :pokemonType="type"
        />
      </div>
      <div class="text-center">
        <p class="title">POKEDEX ENTRY</p>
        <p>
          {{ data?.description }}
        </p>
      </div>
      <div class="text-center">
        <p class="title">ABILITIES</p>
        <div class="d-flex space-around-container">
          <p
            class="round-p"
            v-for="(ability, idx) in data?.abilities"
            :key="idx"
          >
            {{ ability }}
          </p>
        </div>
      </div>
      <div class="text-center">
        <div class="d-flex space-around-container">
          <p class="title">HEIGHT</p>
          <p class="title">WEIGHT</p>
        </div>
        <div class="d-flex space-around-container">
          <p class="round-p">{{ data?.height }}m</p>
          <p class="round-p">{{ data?.weight }}Kg</p>
        </div>
      </div>
      <div class="text-center">
        <div>
          <p class="title">WEAKNESS</p>
          <div class="d-flex">
            <TheTypeButton
              v-for="(weakness, idx) in data?.weaknesses"
              :key="idx"
              :pokemonType="weakness"
            />
          </div>
        </div>
        <div class="text-center">
          <p class="title">STATS</p>
          <div class="d-flex space-around-container">
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
  padding: 4rem 1rem 1rem 1rem;
  background: #fff;
  margin-left: 2rem;
  border-radius: 2rem;
  position: relative;
}
.pokemon-gender {
  position: absolute;
  right: 1.25rem;
  top: 1.25rem;
}
.pokemon-image {
  position: absolute;
  top: -11rem;
  right: 6rem;
  width: 250px;
}
.pokemon-id {
  color: #959595;
  font-size: 1.25rem;
  margin-bottom: 0;
}
.space-around-container {
  justify-content: space-around;
}
.title {
  font-weight: 700;
}
.round-p {
  margin-top: 0;
  background-color: #f7f9fa;
  padding: 0.5rem 1rem;
  border: 2px solid #dbe3ed;
  border-radius: 2rem;
  width: 9rem;
  text-transform: capitalize;
}
.pokemon-name {
  font-size: 1.5rem;
  text-transform: uppercase;
  font-weight: 800;
  text-align: center;
  margin-top: 0;
}
.ribbon {
  font-size: 1rem;
  text-align: center;
  font-weight: bold;
  color: #fff;
}
.ribbon p {
  margin-bottom: 0;
}
.ribbon {
  --f: 0.5em;
  --r: 0.8em;
  position: absolute;
  left: 16px;
  top: calc(-1 * var(--f));
  padding: 0.2em;
  background: #ff9800;
  border-left: var(--f) solid #0005;
  border-bottom: var(--r) solid #0000;
  clip-path: polygon(
    var(--f) 0,
    100% 0,
    100% calc(100% - var(--r)),
    calc(50% + var(--f) / 2) 100%,
    var(--f) calc(100% - var(--r)),
    var(--f) var(--f),
    0 var(--f)
  );
}
</style>
