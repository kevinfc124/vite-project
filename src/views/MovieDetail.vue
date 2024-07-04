<template>
  <div class="container card-container">
    <div v-if="movie" class="mt-4">
      <div class="row">
        <div class="col-md-4">
          <img :src="`https://image.tmdb.org/t/p/w500${movie.poster_path}`" :alt="movie.title" class="img-fluid mb-3 rounded shadow-sm" />
        </div>
        <div class="col-md-8">
          <h1>{{ movie.title }}</h1>
          <p class="mb-4">{{ movie.overview }}</p>
          <p><strong>Lanzamiento:</strong> {{ formattedReleaseDate }}</p>
          <p><strong>Valoración:</strong> {{ movie.vote_average }}/10</p>
          <p><strong>Géneros:</strong> {{ formattedGenres }}</p>
          <div class="d-flex align-items-center">
            <button @click="toggleFavorite(movie)" class="btn btn-heart">
              <i :class="['fas', 'fa-heart', { 'active': isMovieInFavorites(movie) }]"
                 :style="{ color: isMovieInFavorites(movie) ? '#dc3545' : 'white', borderColor: '#dc3545' }"></i>
              {{ isMovieInFavorites(movie) ? 'Quitar de Favoritos' : 'Agregar a Favoritos' }}
            </button>
            <div v-if="showMessage" :class="messageClass" role="alert">
              {{ messageText }}
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      movie: null,
      showMessage: false,
      messageText: '',
      messageClass: '',
    };
  },
  created() {
    this.fetchMovieDetails();
  },
  methods: {
    async fetchMovieDetails() {
      try {
        const response = await fetch(`https://api.themoviedb.org/3/movie/${this.$route.params.id}?api_key=019a94c9702d835a26e8b6ad5b01f04e&language=es-ES`);
        const data = await response.json();
        this.movie = data;
      } catch (error) {
        console.error('Error fetching movie details:', error);
      }
    },
    toggleFavorite(movie) {
      if (this.isMovieInFavorites(movie)) {
        this.removeFavorite(movie);
        this.showMessageWithTimeout('Película quitada de favoritos', 'alert-warning');
      } else {
        this.addFavorite(movie);
        this.showMessageWithTimeout('Película agregada a favoritos', 'alert-success');
      }
    },
    addFavorite(movie) {
      let favoriteMovies = JSON.parse(localStorage.getItem('favoriteMovies')) || [];
      if (!this.isMovieInFavorites(movie)) {
        favoriteMovies.push(movie);
        localStorage.setItem('favoriteMovies', JSON.stringify(favoriteMovies));
      }
    },
    removeFavorite(movie) {
      let favoriteMovies = JSON.parse(localStorage.getItem('favoriteMovies')) || [];
      favoriteMovies = favoriteMovies.filter(favoriteMovie => favoriteMovie.id !== movie.id);
      localStorage.setItem('favoriteMovies', JSON.stringify(favoriteMovies));
    },
    isMovieInFavorites(movie) {
      let favoriteMovies = JSON.parse(localStorage.getItem('favoriteMovies')) || [];
      return favoriteMovies.some(favoriteMovie => favoriteMovie.id === movie.id);
    },
    showMessageWithTimeout(message, messageClass) {
      this.messageText = message;
      this.messageClass = `alert ${messageClass}`;
      this.showMessage = true;
      setTimeout(() => {
        this.showMessage = false;
      }, 3000);
    },
  },
  computed: {
    formattedReleaseDate() {
      if (this.movie && this.movie.release_date) {
        const date = new Date(this.movie.release_date);
        return date.toLocaleDateString('es-ES', { year: 'numeric', month: 'long', day: 'numeric' });
      }
      return '';
    },
    formattedGenres() {
      if (this.movie && this.movie.genres) {
        return this.movie.genres.map(genre => genre.name).join(', ');
      }
      return '';
    },
  },
};
</script>

<style scoped>
.card-container {
  background-color: #ffffff;
  border-radius: 8px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
  padding: 20px;
}

img {
  max-width: 100%;
  height: auto;
  border-radius: 8px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
}

.btn-heart {
  font-size: 1rem;
  padding: 0.5rem 1rem;
  display: flex;
  align-items: center;
  gap: 0.5rem;
  transition: background-color 0.3s ease, color 0.3s ease, border-color 0.3s ease;
  background-color: transparent;
  border: 1px solid #dc3545;
  color: #dc3545;
}

.btn-heart:hover {
  background-color: #dc3545;
  color: white;
}

.btn-heart .fa-heart {
  font-size: 1.5rem;
}

.btn-heart .fa-heart.active {
  color: #dc3545;
}
</style>
