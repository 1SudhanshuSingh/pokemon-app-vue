<script>
import PokemonList from "../components/PokemonList/PokemonList.vue";
import TheHeader from "../components/UI/TheHeader.vue";
export default {
  components: { TheHeader, PokemonList },
};
</script>
<template>
  <div>
    <TheHeader />
    <PokemonList />
  </div>
</template>
<style scoped></style>
