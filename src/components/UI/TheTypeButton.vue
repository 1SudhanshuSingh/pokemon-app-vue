<script>
import pokemonTypeColors from "@/constants/pokemonTypeColor";
export default {
  props: {
    pokemonType: {
      type: String,
      required: true,
    },
  },
  computed: {
    buttonColor() {
      return {
        backgroundColor: pokemonTypeColors[this.pokemonType],
      };
    },
  },
};
</script>

<template>
  <button class="type-button" :style="buttonColor">
    {{ pokemonType }}
  </button>
</template>

<style scoped>
.type-button {
  border: 0;
  padding: 0.5rem 1rem;
  color: #ffffff;
  text-transform: capitalize;
  border-radius: 1rem;
  margin: 0 0.5rem;
}
</style>
