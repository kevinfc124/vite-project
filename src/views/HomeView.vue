<template>
  <div class="container card-container">
    <div class="card p-4">
      <SearchBar @search-results="updateSearchResults" />
  
      <div class="row" v-if="searchResults.length > 0">
        <div class="col-md-12">
          <h2>Resultados de Búsqueda</h2>
          <MovieList :movies="searchResults" @select-movie="selectMovie" />
        </div>
      </div>
  
      <div class="row" v-else>
        <div class="col-md-12">
          <h1>Películas Populares</h1>
          <MovieList :movies="popularMovies" @select-movie="selectMovie" />
        </div>
      </div>
    </div>
  </div>
  </template>
  
  <script>
  import SearchBar from '@/components/SearchBar.vue';
  import MovieList from '@/components/MovieList.vue';
  
  export default {
    components: {
      SearchBar,
      MovieList,
    },
    data() {
      return {
        popularMovies: [],
        searchResults: [],
      };
    },
    created() {
      this.fetchPopularMovies();
    },
    methods: {
      async fetchPopularMovies() {
        try {
          const response = await fetch('https://api.themoviedb.org/3/movie/popular?api_key=019a94c9702d835a26e8b6ad5b01f04e&language=es-ES');
          const data = await response.json();
          this.popularMovies = data.results;
        } catch (error) {
          console.error('Error fetching popular movies:', error);
        }
      },
      updateSearchResults(movies) {
        this.searchResults = movies;
      },
      selectMovie(movie) {
        this.$router.push(`/movie/${movie.id}`);
      },
    },
  };
  </script>
  
  <style scoped>
  .container {
    padding: 20px;
  }
  
  h1, h2 {
    margin-top: 20px;
  }
  </style>
  