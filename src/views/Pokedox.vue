<script>
import PokemonList from "../components/AboutPokemon/PokemonList.vue";
import TheHeader from "../components/UI/TheHeader.vue";
export default {
  components: { TheHeader, PokemonList },
};
</script>
<template>
  <div class="grid-container">
    <TheHeader />
    <PokemonList />
  </div>
</template>
<style scoped>
/* .grid-container{
  display: grid;
  grid-template-columns: auto auto;
} */
</style>
