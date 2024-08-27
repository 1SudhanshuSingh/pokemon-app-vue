<script>
import TheTypeButton from "../UI/TheTypeButton.vue";
export default {
  components: { TheTypeButton },
  props: {
    listItem: {
      type: Object,
      required: true,
      default: () => null,
    },
  },
  emits: ["onClick"],
  data() {
    return {};
  },
  methods: {
    selectedPokemonInfo(id) {
      this.$emit("onClick", id);
    },
  },
};
</script>
<template>
  <div
    :key="listItem?.id"
    class="pokemon-card"
    @click="selectedPokemonInfo(listItem?.id)"
  >
    <img
      :src="listItem?.sprites.other.home.front_default"
      :alt="listItem?.name"
      class="image"
    />
    <p>{{ listItem?.name }}</p>
    <div class="row justify-align-center">
      <TheTypeButton
        v-for="type in listItem?.types"
        :key="type?.type?.name"
        :pokemonType="type?.type?.name"
      />
    </div>
  </div>
</template>
<style scoped>
.pokemon-card {
  background-color: #fff;
  border-radius: 3rem;
  box-shadow: 8px 8px 4px #d1d1d1;
  padding: 1.25rem;
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
