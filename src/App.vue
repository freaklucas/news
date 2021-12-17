<template>
  <Layout>
    <h2 class="mb-8 text-4xl font-semibold text-center capitalize">
      News Section: <span class="text-gray-700">{{ section }}</span>
    </h2>
    <NewsFilter v-model="section" />
    <NewsList :posts="posts" />
  </Layout>
</template>

<script>
import Layout from "./components/Layout.vue";
import NewsFilter from "./components/NewsFilter.vue";
import NewsList from "./components/NewsList.vue";

import axios from "axios";
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
          console.log("servidor off:", err);
        } else if (err.request) {
          console.log("erro de rede:", err);
        } else {
          console.log("erro do cliente", err);
        }
      }
    },
  },
  mounted() {
    this.fetchNews();
  },
};
</script>