<script>
import Images from "@/constants/images";
export default {
  data() {
    return {
      navItems: [
        { id: 1, title: "Home", icon: Images.HomeIcon },
        { id: 2, title: "Pokedox", icon: Images.PokedoxIcon },
        { id: 2, title: "Videogame", icon: Images.VideogameIcon },
        { id: 3, title: "GCC Pokemon", icon: Images.GCCIcon },
        { id: 4, title: "TV Pokemon", icon: Images.TVIcon },
        { id: 5, title: "Play Pokemon", icon: Images.PlayIcon },
        { id: 6, title: "News", icon: Images.NewsIcon },
      ],
    };
  },
};
</script>
<template>
  <header class="container">
    <div class="nav-container">
      <div class="nav-item" v-for="item in navItems" :key="item.id">
        <img :src="item.icon" alt="" srcset="" />
        <p class="title">{{ item.title }}</p>
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
  border-radius: 2rem;
  box-shadow: 8px 8px 4px #d1d1d1;
}
.nav-item {
  padding: 2rem;
  display: flex;
  align-items: center;
  justify-content: center;
}
.nav-item img {
  width: 3rem;
}
.nav-item .title{
  margin-left: 0.5rem;
}
</style>
 
