<template>
  <div class="row row-cols-1 row-cols-md-3 g-4">
    <div v-for="movie in movies" :key="movie.id" class="col">
      <div class="card h-100" @click="selectMovie(movie)">
        <img :src="`https://image.tmdb.org/t/p/w500${movie.poster_path}`" :alt="movie.title" class="card-img-top" />
        <div class="card-body">
          <h5 class="card-title">{{ movie.title }}</h5>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    movies: {
      type: Array,
      required: true,
    },
  },
  methods: {
    selectMovie(movie) {
      this.$emit('select-movie', movie);
    },
  },
};
</script>
