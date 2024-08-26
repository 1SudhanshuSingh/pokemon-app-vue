<script>
export default {
  data() {
    return {
      navItems: [
        "Home",
        "Pokedox",
        "Videgame",
        "GCC Pokemon",
        "TV Pokemon",
        "Play Pokemon",
        "News",
      ],
    };
  },
};
</script>
<template>
  <header class="container">
    <div class="nav-container">
      <div class="nav-item" v-for="(item, idx) in navItems" :key="idx">
        {{ item }}
      </div>
    </div>
  </header>
</template>
<style scoped>
.nav-container {
  display: flex;
  width: 100%;
  justify-content: space-around;
  background-color: #fff;
  margin-top: 2rem;
}
.nav-item {
    padding: 2rem;
}
</style>
 
