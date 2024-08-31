<script>
import TypeIcons from "@/constants/typeSvg";

export default {
  props: {
    pokemonType: {
      type: String,
      required: true,
    },
  },
  data() {
    return {
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
};
</script>

<template>
  <div class="type--icon">
    <img
      :class="pokemonType"
      :src="typeIcons[pokemonType]"
      :alt="pokemonType"
      :title="pokemonType"
    />
    <p class="type--title">{{ pokemonType }}</p>
  </div>
</template>

<style scoped>
.type--title {
  text-transform: capitalize;
  margin-top: 0;
  font-size: 0.75rem;
}
</style>
