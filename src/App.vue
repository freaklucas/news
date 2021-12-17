<template>
  <Layout>
    <h2 class="mb-8 text-4xl font-bold text-center capitalize">
      News Section : <span class="text-green-700">{{ section }}</span>
    </h2>
    <NewsFilter v-model="section" :fetch="fetchNews" />
    <NewsList v-if="!loading && !error" :posts="posts" />
    <div class="mt-40" v-if="loading">
      <p class="text-6xl font-bold text-center text-gray-500 animate-pulse">
        Loading...
      </p>
    </div>
    <div class="mt-12 bg-red-50" v-if="error">
      <h3 class="px-4 py-1 text-4xl font-bold text-white bg-red-800">
        {{ error.title }}
      </h3>
      <p class="p-4 text-lg font-bold text-red-900">{{ error.message }}</p>
    </div>
  </Layout>
</template>

<script>
import axios from "axios";
import Layout from "./components/Layout.vue";
import NewsFilter from "./components/NewsFilter.vue";
import NewsList from "./components/NewsList.vue";
const api = import.meta.env.VITE_NYT_API_KEY;

export default {
  components: {
    Layout,
    NewsFilter,
    NewsList,
  },
  data() {
    return {
      section: "home",
      posts: [],
      loading: false,
      error: null,
    };
  },
  methods: {
    extractImage(post) {
      const defaultImg = {
        url: "http://placehold.it/210x140?text=N/A",
        caption: post.title,
      };
      if (!post.multimedia) {
        return defaultImg;
      }
      let imgObj = post.multimedia.find(
        (media) => media.format === "mediumThreeByTwo210"
      );
      return imgObj ? imgObj : defaultImg;
    },
    async fetchNews() {
      try {
        this.error = null;
        this.loading = true;
        const url = `https://api.nytimes.com/svc/topstories/v2/${this.section}.json?api-key=${api}`;
        const response = await axios.get(url);
        const results = response.data.results;
        this.posts = results.map((post) => ({
          title: post.title,
          abstract: post.abstract,
          url: post.url,
          thumbnail: this.extractImage(post).url,
          caption: this.extractImage(post).caption,
          byline: post.byline,
          published_date: post.published_date,
        }));
      } catch (err) {
        if (err.response) {
          this.error = {
            title: "Server Response",
            message: err.message,
          };
        } else if (err.request) {
          this.error = {
            title: "Unable to Reach Server",
            message: err.message,
          };
        } else {
          this.error = {
            title: "Application Error",
            message: err.message,
          };
        }
      }
      this.loading = false;
    },
  },
  mounted() {
    this.fetchNews();
  },
};
</script>