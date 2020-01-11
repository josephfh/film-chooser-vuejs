<template>
  <div>
    <div
      class="fixed bg-gray-800 bottom-0 h-auto m-2 mb-16 p-8 py-4 left-0 right-0 shadow-md z-30"
    >
      <div class="flex">
        <div>
          <h1>Add a film</h1>
          <div>
            <input
              class="text-black p-1"
              type="search"
              v-model="search"
              ref="search"
              placeholder="Title of film"
            />
            <a
              :href="
                `https://www.themoviedb.org/search?query=${search.replace(
                  ' ',
                  '%20'
                )}&language=en-US`
              "
              target="_blank"
              class="border border-white p-1"
              >Search on TMBD</a
            >
          </div>
          <div>
            <input
              class="text-black p-1"
              type="text"
              v-model="newValue"
              ref="newInput"
              placeholder="Type in TMBD id"
              v-on:keyup.enter="addFilm(newValue)"
            />
            <button @click="addFilm(newValue)" class="border border-white p-1">
              Add
            </button>
          </div>
        </div>
        <div>
          <slot />
        </div>
      </div>
    </div>
    <div class="opacity-75 bg-black fixed top-0 left-0 right-0 bottom-0 z-20" />
  </div>
</template>

<script>
export default {
  data: function() {
    return {
      newValue: null,
      search: ""
    };
  },
  name: "Setup",
  methods: {
    addFilm(id) {
      this.$parent.addFilm(id);
      this.newValue = null;
      this.search = "";
      this.$refs.search.focus();
    }
  }
};
</script>
