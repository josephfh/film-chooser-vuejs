<template>
  <div id="app" class="bg-gray-900 h-screen text-white">
    <swiper
      :options="swiperOption"
      ref="mySwiper"
      class="h-screen w-full fixed top-0 left-0"
    >
      <swiper-slide v-for="film in films" :key="film.id">
        <div class="flex">
          <div class="flex-1 flex justify-center">
            <img
              :src="
                `https://image.tmdb.org/t/p/w370_and_h556_bestv2${film.posterPath}`
              "
              class="h-screen w-auto"
            />
          </div>
          <div class="p-4 flex-1">
            <h1 class="mr-2 text-5xl uppercase leading-tight tracking-wider">
              <a @click="addPoint(film.title)">{{ film.title }}</a>
            </h1>
            <div class="mr-2 text-4xl">
              {{ film.year }}
              <span class="ml-2 opacity-25">{{ film.runtime }}mins</span>
            </div>
            <div class="flex text-4xl opacity-50">
              <span
                class="mr-2"
                v-for="genre in film.genres"
                :key="`${film.id}${genre.id}`"
                >{{ genre.name }}
              </span>
            </div>
            <div class="text-4xl italic text-yellow-500">
              {{ film.tagline }}
            </div>
            <div class="text-3xl">{{ film.overview }}</div>
          </div>
        </div>
      </swiper-slide>
    </swiper>
    <Setup v-if="setupVisible"
      ><div>{{ error }}</div>
      <div class="flex">
        <ul>
          <li v-for="film in films" :key="film.id">
            <div class="flex">
              <div class="flex flex-grow">
                <div class="mr-8">{{ film.title }} {{ film.year }}</div>
              </div>
              <button
                @click="deleteFilm(film.id)"
                class="border border-white flex h-8 w-8 mr-2 align-middle justify-center"
              >
                X
              </button>
            </div>
          </li>
        </ul>
        <div>
          <ul class="bg-gray-600 shadow text-2xl mt-32">
            <li v-for="(points, item) in points" :key="`points${item.id}`">
              {{ item }}: {{ points }}
            </li>
          </ul>
          <button class="border border-white p-1" @click="clearPoints()">
            Clear points
          </button>
        </div>
      </div>
      <div class="fixed flex left-0 top-0 z-50">
        <div v-for="film in films" :key="film.id">
          <img
            :src="
              `https://image.tmdb.org/t/p/w370_and_h556_bestv2${film.posterPath}`
            "
            class="h-64 w-auto"
          />
        </div>
      </div>
    </Setup>
    <button
      class="rounded-full opacity-25 border-white border fixed bottom-0 right-0 w-8 h-8 mr-2 mb-2 z-50"
      @click="setupVisible = !setupVisible"
    />
  </div>
</template>

<script>
import Setup from "./components/Setup.vue";

import "swiper/dist/css/swiper.css";

import { swiper, swiperSlide } from "vue-awesome-swiper";

export default {
  name: "app",
  components: {
    Setup,
    swiper,
    swiperSlide
  },
  data: function() {
    return {
      tmdbApiKey: process.env.VUE_APP_TMDB_API_KEY,
      error: null,
      films: [],
      points: {},
      setupVisible: true,
      swiperOption: {
        slidesPerView: 1,
        spaceBetween: 30,
        loop: true,
        navigation: {
          nextEl: ".swiper-button-next",
          prevEl: ".swiper-button-prev"
        }
      }
    };
  },
  computed: {
    swiper() {
      return this.$refs.mySwiper.swiper;
    }
  },
  methods: {
    addFilm(id) {
      fetch(
        `https://api.themoviedb.org/3/movie/${id}?api_key=${this.tmdbApiKey}`
      )
        .then(response => response.json())
        .then(data => {
          this.films.push({
            genres: data.genres,
            id: id,
            overview: this.shorten(data.overview, 250) + "...",
            posterPath: data.poster_path,
            runtime: data.runtime,
            tagline: data.tagline,
            title: data.title,
            year: data.release_date.substring(0, 4)
          });
        })
        .catch(error => {
          this.error = "not found";
          console.log(error);
        });
    },
    deleteFilm(id) {
      this.films = this.films.filter(film => film.id != id);
    },
    addPoint(id) {
      if (!this.points[id]) {
        this.points[id] = 1;
      } else {
        this.points[id] = this.points[id] + 1;
      }
    },
    clearPoints() {
      this.points = {};
    },
    shorten(str, maxLen, separator = " ") {
      if (str.length <= maxLen) return str;
      return str.substr(0, str.lastIndexOf(separator, maxLen));
    }
  }
};
</script>
