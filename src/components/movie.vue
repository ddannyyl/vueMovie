<script setup>
import { ref } from "vue";
import axios from "axios";

const select = ref();
const movie = ref(null);

// const getTMDBData = async (id) => {
//   return (
//     await axios.get(
//       `https://api.themoviedb.org/3/movie/${id}?api_key=${
//         import.meta.env.VITE_API_KEY
//       }&language=en-US&adult=false`
//     )
//   ).data;
// };
const getTMDBData = async (id) => {
  return (
    await axios.get(
      `https://api.themoviedb.org/3/movie/${id}?api_key=${
        import.meta.env.VITE_API_KEY
      }&language=en-US&adult=false&append_to_response=videos`
    )
  ).data;
};

const findMovie = async () => {
  movie.value = await getTMDBData(select.value);
};

// const hasTrailer = () => {
//   return (
//     movie.value && movie.value.videos && movie.value.videos.results.length > 0
//   );
// };
const hasTrailer = () => {
  return movie.value && movie.value.videos && movie.value.videos.results && movie.value.videos.results.length > 0;
};

const getTrailerUrl = () => {
  if (hasTrailer()) {
    const trailer = movie.value.videos.results.find(
      (trailer) => trailer.type === "Trailer"
    );
    if (trailer) {
      return `https://www.youtube.com/embed/${trailer.key}`;
    }
  }
  return ""; // Return an empty string if there is no trailer
};

// const openTrailer = () => {
//   const trailerUrl = getTrailerUrl();
//   if (trailerUrl) {
//     window.open(trailerUrl);
//   } else if (hasTrailer()) {
//     const trailerKey = movie.value.videos.results[0].key;
//     window.open(`https://www.youtube.com/watch?v=${trailerKey}`, "_blank");
//   }
// };
const openTrailer = () => {
  const trailerUrl = getTrailerUrl();
  if (trailerUrl) {
    window.open(trailerUrl, "_blank");
  } else if (hasTrailer()) {
    const trailerKey = movie.value.videos.results[0].key;
    window.open(`https://www.youtube.com/watch?v=${trailerKey}`, "_blank");
  }
};
</script>

<template>
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
  </head>

  <body>
    <header>
      <h1>Movies</h1>
    </header>
    <div id="movies"></div>
    <div class="navBar">
      <label for="options">Choose a movie:</label>
      <select v-model="select" id="options">
        <option value="505642">Black Panther: Wakanda Forever</option>
        <option value="980078">Winnie the Pooh: Blood and Honey</option>
        <option value="1033219">Attack on Titan</option>
        <option value="677179">Creed III</option>
        <option value="594767">Shazam! Fury of the Gods</option>
        <option value="640146">Ant-Man and the Wasp: Quantumania</option>
        <option value="1077280">Die Hart</option>
        <option value="758009">Shotgun Wedding</option>
        <option value="76600">Avatar: The Way of Water</option>
        <option value="315162">Puss in Boots: The Last Wish</option>
      </select>
      <button @click="findMovie" id="getMovie">Get</button>
    </div>

      <div class="movieInfo">
    <div v-if="movie" class="moviePoster">
      <img :src="`https://image.tmdb.org/t/p/w500${movie.poster_path}`" />
        </div>
    <div v-if="hasTrailer()" class="trailer">
      <button @click="openTrailer">Watch Trailer</button>
      <iframe v-if="getTrailerUrl()" :src="getTrailerUrl()" allowfullscreen></iframe>
    </div>
      <div v-else>
        <iframe :src="trailerKey()" v-if="hasTrailer()"></iframe>
      </div>
    </div>

    <div v-if="movie">
      <h1>Movie: {{ movie.title }}</h1>
      <p>Number of Votes: {{ movie.id }}</p>
      <p>Release date: {{ movie.release_date }}</p>
      <p>Overview: {{ movie.overview }}</p>
      <p>Movie duration: {{ movie.runtime }} mins</p>
      <p>Average rating: {{ movie.vote_average }}/10</p>
      <p>Number of Votes: {{ movie.vote_count }} votes</p>
      <p>Production Company: {{ movie.production_companies[0].name }}</p>
      <p>Genre: {{ movie.genres[0].name }}</p>
    </div>
  </body>
</template>

<style scoped>
* {
  font-family: "Poppins", sans-serif;
  padding: 0;
  margin: 0;
  box-sizing: border-box;
  background-color: rgb(220, 240, 215);
}

.navBar {
  position: relative;
  text-align: center;
}

img {
  width: 300px;
}

button {
  border-radius: 0.5rem;
  color: rgb(0, 0, 0);
  padding: 0.5rem;
  align-self: end;
  font-weight: bold;
  border: 2px solid black;
}
</style>
