<template>
    <div class="container card-container">
  <div class="container mt-4">
    <h2 class="mb-4">Películas Favoritas</h2>
    <div v-if="favoriteMovies.length === 0" class="alert alert-info" role="alert">
      No tienes películas favoritas aún.
    </div>
    <div v-else>
      <MovieList :movies="favoriteMovies" @select-movie="selectMovie" />
    </div>
  </div>
</div>
</template>

<script>
import MovieList from '@/components/MovieList.vue';

export default {
  components: {
    MovieList,
  },
  data() {
    return {
      favoriteMovies: [],
    };
  },
  created() {
    this.loadFavoriteMovies();
  },
  methods: {
    loadFavoriteMovies() {
      this.favoriteMovies = JSON.parse(localStorage.getItem('favoriteMovies')) || [];
    },
    selectMovie(movie) {
      if (!this.isMovieInFavorites(movie)) {
        this.favoriteMovies.push(movie);
        this.updateLocalStorage();
      }
      this.$router.push(`/movie/${movie.id}`);
    },
    isMovieInFavorites(movie) {
      return this.favoriteMovies.some(favoriteMovie => favoriteMovie.id === movie.id);
    },
    updateLocalStorage() {
      localStorage.setItem('favoriteMovies', JSON.stringify(this.favoriteMovies));
    },
  },
};
</script>

<style scoped>
.card-container {
  background-color: rgb(213, 213, 213);
  border-radius: 8px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
  padding: 20px;
}
</style>
