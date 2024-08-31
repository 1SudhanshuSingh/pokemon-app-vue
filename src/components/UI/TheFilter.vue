<script>
import { pokemonFilters } from "@/constants/pokemonFilters";
export default {
  emits: ["getOrder", "handlePagination", "getAppliedFilter"],
  data() {
    return {
      order: "asc",
      fromPage: 0,
      toPage: 15,
      selectedFilters: {
        type: null,
        ability: null,
        habitat: null,
        color: null,
        legendaryStatus: null,
        shape: null,
      },
      pokemonFilters: pokemonFilters,
    };
  },
  watch: {
    order(newOrder) {
      this.$emit("getOrder", newOrder);
    },
    selectedFilters: {
      deep: true,
      handler(appliedFilter) {
        this.$emit("getAppliedFilter", appliedFilter);
      },
    },
  },
  methods: {
    handlePagination() {
      this.$emit("handlePagination", this.offset, this.limit);
    },
    handleBlur() {
      this.handlePagination();
    },
    handleNext() {
      this.fromPage = this.toPage + 1;
      this.toPage = this.fromPage + 14;
      this.$emit("handleNext", this.fromPage, this.toPage);
    },
    handlePrev() {
      if (this.fromPage > 1) {
        this.toPage = this.fromPage - 1;
        this.fromPage = Math.max(this.toPage - 14, 0);
        this.$emit("handlePrev", this.fromPage, this.toPage);
      }
    },
  },
};
</script>
<template>
  <div>
    <div class="filter-container mt-2">
      <div class="dropdown-form">
        <select v-model="order">
          <option value="asc">Ascending</option>
          <option value="desc">Descending</option>
        </select>
      </div>
      <div class="pagination-control">
        <div class="page-btn" @click="handlePrev">&lt;</div>
        <div>
          <input type="number" class="page-input" v-model="fromPage" />
          <input
            type="number"
            class="page-input"
            v-model="toPage"
            @blur="handleBlur"
          />
        </div>
        <div class="page-btn" @click="handleNext">&gt;</div>
      </div>
    </div>
    <div class="filter-container mt-2">
      <div class="dropdown-form">
        <p>Type</p>
        <select v-model="selectedFilters.type">
          <option
            v-for="type in pokemonFilters.type"
            :key="type.id"
            :value="type.value"
          >
            {{ type.key }}
          </option>
        </select>
      </div>
      <div class="dropdown-form">
        <p>Ability</p>
        <select v-model="selectedFilters.ability">
          <option
            v-for="ability in pokemonFilters.ability"
            :key="ability.id"
            :value="ability.value"
          >
            {{ ability.key }}
          </option>
        </select>
      </div>
      <div class="dropdown-form">
        <p>Habitat</p>
        <select v-model="selectedFilters.habitat">
          <option
            v-for="habitat in pokemonFilters.habitat"
            :key="habitat.id"
            :value="habitat.value"
          >
            {{ habitat.key }}
          </option>
        </select>
      </div>
      <div class="dropdown-form">
        <p>Color</p>
        <select v-model="selectedFilters.color">
          <option
            v-for="color in pokemonFilters.color"
            :key="color.id"
            :value="color.value"
          >
            {{ color.key }}
          </option>
        </select>
      </div>
      <div class="dropdown-form">
        <p>Status</p>
        <select v-model="selectedFilters.legendaryStatus">
          <option
            v-for="legendaryStatus in pokemonFilters.legendaryStatus"
            :key="legendaryStatus.id"
            :value="legendaryStatus.value"
          >
            {{ legendaryStatus.key }}
          </option>
        </select>
      </div>
      <div class="dropdown-form">
        <p>Shape</p>
        <select v-model="selectedFilters.shape">
          <option
            v-for="shape in pokemonFilters.shape"
            :key="shape.id"
            :value="shape.value"
          >
            {{ shape.key }}
          </option>
        </select>
      </div>
    </div>
  </div>
</template>
<style scoped>
.filter-container {
  display: flex;
  justify-content: space-between;
}
.dropdown-form {
  padding: 1rem 1.5rem;
  border-radius: 1rem;
  background: #fff;
  box-shadow: 8px 8px 4px #d1d1d1;
}
.dropdown-form select {
  border: 0;
  outline: none;
}
.pagination-control {
  display: flex;
  align-items: center;
}
.page-input {
  padding: 1rem 2rem;
  border-radius: 1rem;
  border: 0;
  width: 3rem;
  margin: 0 0.5rem;
}
.page-btn {
  background: #fff;
  padding: 0.5rem 0.75rem;
  border-radius: 2rem;
}
</style>
