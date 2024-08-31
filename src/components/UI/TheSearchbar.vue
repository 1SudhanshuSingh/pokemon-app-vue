<script>
import Images from "@/constants/images";
export default {
  emits: ["handleSearch"],
  data() {
    return {
      searchIcon: Images.PokeballSearch,
      searchQuery: "",
    };
  },
};
</script>
<template>
  <div class="search-container">
    <input
      type="search"
      class="searchbar"
      placeholder="Search your pokemon here"
      v-model="searchQuery"
      @keyup.enter="$emit('handleSearch', searchQuery)"
    />
    <img
      :src="searchIcon"
      alt=""
      srcset=""
      class="search-icon"
      @click="$emit('handleSearch', searchQuery)"
    />
  </div>
</template>
<style scoped>
.search-container {
  display: flex;
  width: 93%;
  border: 0;
  padding: 1rem 2rem;
  border-radius: 1rem;
  background: #fff;
  box-shadow: 8px 8px 4px #d1d1d1;
}
.searchbar {
  width: 99%;
  border: 0;
  font-size: 1rem;
  background-color: none;
}
.searchbar:focus {
  outline: none;
}
.search-icon {
  margin-left: 0.5rem;
  width: 48px;
}
</style>
