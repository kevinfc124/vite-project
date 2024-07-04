<template>
  <div class="container mt-5">
    <!-- Sección de Búsqueda -->
    <div class="input-group mb-4 shadow-sm">
      <input
        type="text"
        class="form-control"
        v-model="query"
        @keyup.enter="searchMovies"
        placeholder="Buscar películas..."
        aria-label="Buscar películas"
        aria-describedby="button-search"
      />
      <button class="btn btn-primary" type="button" id="button-search" @click="searchMovies" :disabled="loading">
        {{ loading ? 'Buscando...' : 'Buscar' }}
      </button>
    </div>

    <!-- Sección de Filtro por Categorías -->
    <div class="mb-4">
      <select class="form-select" v-model="selectedCategory" @change="filterByCategory">
        <option value="">Todas las Categorías</option>
        <option v-for="category in categories" :key="category.id" :value="category.id">
          {{ category.name }}
        </option>
      </select>
    </div>

    <!-- Mensajes de Error y Resultados -->
    <div v-if="error" class="alert alert-danger mt-4" role="alert">
      {{ error }}
    </div>
    <div v-if="noResults" class="alert alert-warning mt-4" role="alert">
      No se encontraron películas para el término de búsqueda "{{ query }}".
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      query: '',
      selectedCategory: '',
      categories: [
        { id: '28', name: 'Acción' },
        { id: '12', name: 'Aventura' },
        { id: '16', name: 'Animación' },
        { id: '35', name: 'Comedia' },
        { id: '18', name: 'Drama' },
        { id: '27', name: 'Terror' },
      ],
      loading: false,
      error: null,
      noResults: false,
    };
  },
  methods: {
    async searchMovies() {
      this.error = null;
      this.noResults = false;

      try {
        let url = 'https://api.themoviedb.org/3/search/movie';
        const params = {
          api_key: '019a94c9702d835a26e8b6ad5b01f04e',
          language: 'es-ES',
          query: this.query
        };

        const response = await fetch(url + '?' + new URLSearchParams(params));
        const data = await response.json();
        
        if (data.results.length > 0) {
          this.$emit('search-results', data.results);
        } else {
          this.noResults = true;
        }
      } catch (error) {
        this.error = 'Error al buscar películas.';
        console.error('Error fetching search results:', error);
      } finally {
        this.loading = false;
      }
    },
    async filterByCategory() {
      this.error = null;
      this.noResults = false;

      try {
        let url = 'https://api.themoviedb.org/3/discover/movie';
        const params = {
          api_key: '019a94c9702d835a26e8b6ad5b01f04e',
          language: 'es-ES',
          with_genres: this.selectedCategory
        };

        const response = await fetch(url + '?' + new URLSearchParams(params));
        const data = await response.json();
        
        if (data.results.length > 0) {
          this.$emit('search-results', data.results);
        } else {
          this.noResults = true;
        }
      } catch (error) {
        this.error = 'Error al buscar películas por categoría.';
        console.error('Error fetching movies by category:', error);
      } finally {
        this.loading = false;
      }
    },
  },
  watch: {
    query() {
      if (!this.query.trim()) {
        this.noResults = false;
      }
    },
    selectedCategory() {
      if (this.selectedCategory) {
        this.filterByCategory();
      } else {
        this.searchMovies();
      }
    }
  }
};
</script>

<style scoped>
.input-group > .btn {
  height: auto;
}
.input-group .btn {
  align-self: stretch;
}
.input-group .form-select {
  max-width: 200px;
}
.alert {
  margin-top: 10px;
}
</style>
